---
title: "Who's Birthday is it in n number of days?"
date: 2013-03-11 07:00:00
tags: sql
alias:
---

A friend of mine recently asked me the following question: "_I have a table with names and birthdays. How do I find who's birthday is coming up in the next 15 days_". Initially this it appears to be a simple question but it's a bit more complex than I originally thought.

I was going to post my solution here, then [James Murtagh](https://twitter.com/AllThingsOracle), from Red Gate, offered me two five-user licenses for their new product called [Source Control for Oracle](http://www.red-gate.com/source-control-for-oracle) to give away to the readers of this blog. Instead, I'm going to run an informal contest to give away these sets of licenses (each valued at $1475). As with all contests, please read the [terms and conditions](http://www.red-gate.com/products/oracle-development/source-control-for-oracle/source-control-oracle-prize-terms) from Redgate.

So here's the question (similar to my friend's question about birthday's but on the common `EMP` table): _Suppose that I'm the HR manager and am planning to recognize the anniversary date that each employee was hired on. I'd like to know all the employees who's anniversary hire date is in the next 30 days._

Please post your solution in the comments section below. Every answer with a correct solution will have their name entered into the draw. Answers must be submitted by end of day on Friday March 15th. I'll announce the winners next week.

Notes:

- Use `SYSDATE` for today's date. I'll just alter the [`FIXED_DATE`](http://www.talkapex.com/2012/08/setting-sysdate-in-oracle-for-testing.html) setting in oracle to set the `SYSDATE` value for my testing.  
- Write your query for the default `EMP` table. If you don't have the `EMP` table in your schema [this article](http://www.scribd.com/doc/55324111/Oracle-Emp-Table-Script) contains the scripts to generate it.

I'm looking forward to everyone's solutions!

_**Update**: Please read the [follow up post](http://www.talkapex.com/2013/03/and-winner-is.html) to see how I tested this solution.
_
