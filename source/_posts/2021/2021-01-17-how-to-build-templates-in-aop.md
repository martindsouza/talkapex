---
title: How to build templates in APEX Office Print
tags:
  - aop
date: 2021-01-17 17:24:25
alias:
---


If you ever need to generate PDF reports, Excel files, or Word documents based on data from your APEX application (or from within your Oracle database) [APEX Office Print](https://www.apexofficeprint.com/) (AOP) is the tool for you. If you've never used or heard of AOP before you should review it before continuing with this article.

This is the desired outcome of the article below. To summarize the solution will allow me to quickly edit a Word template using Office 365. Then view the generated PDF in APEX with a minimal amount of effort.


{% asset_img aop-template.gif %}

## Background

AOP has two common uses cases (personal opinion). The first is providing users the ability to download their reports exactly as they're shown in any format they'd like. For example if they build an Interactive Report (IR) with row highlighting etc and click "Download", AOP will make the report look exactly as the user defined it. The second, which this article focuses on, is to create customizable documents based on data in Oracle. A good example of this is generating a invoice for a customer.


## Problem

Using the later case, to generate a PDF invoice you need to create a Word or Excel file and use substitution string for AOP to replace data with. For example, to show the Invoice Number based on the column `invoice_num` you would reference `{invoice_num}` in your Word document. AOP with then "fill in" the substitution strings with your data. In this case the process is as follows:

1. Define your query (with all the data)
  - One time task unless more data is required 
1. Upload Word template file
  - They're various options as to where to store a file (more on this later)
1. Generate the PDF
  - This will involved downloading and then opening the PDF on your laptop


When initially building a template file the last two steps can happen many times. I.e. I want to see what the invoice would look like if I move some text over a bit. I'd need to re-upload the file then re-generate and download the PDF. This process has a few problems:

- It's not good for multiple people working on the same template at the same time
- There's lots of steps involved once making a change to the template to see the resulting PDF

## Solution

To resolve the issues described above I created the following process to allow me to quickly build and view invoices using AOP and APEX. These steps require that you have an [Office 365](https://www.office.com) account. (*Note: if using a coporate Office 365 you'll need the ability to share files with people outside of your organization. If you're not allowed to use a personal account to host the template file*)

### Host Template on OneDrive

- Go to [One Drive](https://onedrive.live.com/) and upload a new Word document
  - **Note:** *For some reason when creating the Word document directly in One Drive AOP can't process the file. To get around this create a Word document on your desktop and upload*
  - *Note: the same steps can be used for any type of Office document (ex: Excel)*
- Generate Share URL
  - This will be a link to give others to work on modifying the Word template file
  - We'll refer to this URL as the "Share URL"
  - Right click on the file and click `Share`

{% asset_img office-365-share-01.png %}

  - A new modal box pops up. Make sure to select the appropriate permissions (not critical that everyone can edit but does make it easy to share the link)
  - Select `Copy link` and save this link somewhere
    - In my example the link is: `https://1drv.ms/w/s!AmD6WK_LJIvHq8xyy0JR-JT6ihJdPQ?e=xC88lZ`

{% asset_img office-365-share-02.png %}

- Generate Embed URL
  - This will be used by your database to download the template file and send to AOP
  - We'll refer to this URL as the "Embed URL"
  - Right click on the file and click `Embed`

{% asset_img office-365-embed-01.png %}

  - A new slider pops up on the right hand side. Click `Generate`

{% asset_img office-365-embed-02.png %}

  - Copy the `iframe` HTML tag and save this link
    - In my example the HTML is: `<iframe src="https://onedrive.live.com/embed?cid=C78B24CBAF58FA60&resid=C78B24CBAF58FA60%21714354&authkey=AKLKkXB0W5OfUZ4&em=2" width="476" height="288" frameborder="0" scrolling="no"></iframe>`

{% asset_img office-365-embed-03.png %}



### APEX Setup

Make sure that your APEX application has the latest [AOP plugins](http://www.apexofficeprint.com/docs/#apex-plug-in) installed. 

Create a new page with a new region. Add the following text page items (*Note: you can change to hidden later on / load from a table*)

- `P3120_ONEDRIVE_EMBED_IFRAME`

In the region add the following in the `Source > Text` box:

`<div id="pdf" data-aop-inline-pdf="onClick: Generate PDF" style="width:100%;height:600px;"></div>`

{% asset_img apex-setup-region.png %}


Create a new region button called `GENERATE_PDF`
- Set the `Button Position` to `Edit`
- Right click on the button and `Create Dynamic Action`
  - `Name`: `onClick: Generate PDF`
    - Note: **Do Not** this name must match the `data-aop-inline-pdf` from the previous `div` tag
  - Add the following actions:
    - `Execute Server Side Code`
      - Items to Submit: `P3120_ONEDRIVE_EMBED_IFRAME`
      - PL/SQL Code:

```sql
declare
  l_download_url varchar2(4000);
  l_blob blob;
  l_file_name varchar2(4000);
begin
  -- Need to replace ampersand with amp; for XML parsing
  -- Need to do in SQL as extractvalue not PL/SQL
  select replace(extractvalue(xmltype(replace(:p3120_onedrive_embed_iframe, chr(38), chr(38) || 'amp;')), '/iframe/@src'), 'embed', 'download')
  into l_download_url
  from dual;
  

  -- Need to download the blob here as AOP expects a filename in the URL. The Office downloads don't have that.

  -- Download the file into a blob
  l_blob := apex_web_service.make_rest_request_b(
    p_url => l_download_url,
    p_http_method => 'GET');

  -- Need to extract filename so that we can get the file extension
  for i in 1.. apex_web_service.g_headers.count loop
    if apex_web_service.g_headers(i).name = 'Content-Disposition' then
      -- Value will look like: attachment; filename="wo-test.docx"
      l_file_name := trim('"' from  regexp_substr(apex_web_service.g_headers(i).value, '".*"'));
      exit; -- Don't need to parse headers anymore
    end if;
  end loop;

  -- Store in Collection for retrieval from AOP
  apex_collection.create_or_truncate_collection(
    p_collection_name => 'AOP_TEMPLATE');

  -- Load
  apex_collection.add_member(
    p_collection_name => 'AOP_TEMPLATE',
    p_c001 => l_file_name,
    p_c002 => apex_string_util.get_file_extension(l_file_name),
    p_blob001 => l_blob
  );
end;
```

{% asset_img apex-da-execute-plsql.png %}


- Plugin: `UC - APEX Office Print (AOP) - DA [Plug-In]`
  - *Note: You'll to have installed the AOP Plugins for this to show up in list*
  - Settings:
    - Template Type: `SQL`
    - Template Source:

```sql
select
  ac.c002 file_ext,
  ac.blob001 blob_contents
from apex_collections ac
where 1=1
  and ac.collection_name = 'AOP_TEMPLATE'
```
  - Data Type: `SQL`
  - Data Source:

```sql
-- Change this to reference you tables etc
select
  'demo' as "filename",
  cursor(
    select to_char(sysdate, 'DD-MON-YYYY') as "today"
    from dual
  ) as "data"
from dual
```

  - Output Type: `PDF`
    - *Note you can change this to a different type of file later but for the preview in APEX, PDF is required*
  - Output To: `Inline Region (pdf/html/md/txt only)`
    - *This is what will make the PDF show up in APEX*

{% asset_img apex-da-aop-plugin.png %}


Using the iFrame URL, users should be able to see the preview in APEX based on changes made in Office 365 (see video at top of article)