TODO Steps and any changes I did


# New posts

```bash
hexo new draft TITLE


# Once ready to post
hexo publish FILENAME

```

# Testing
This uses the `npm install hexo-browsersync --save` plugin

`hexo server --draft`

# Build

```bash
git clone TODO ssh github
npm install
```

## Hexo Specific

```bash

# Generate content
hexo generate

# Deployment
hexo deploy -g
```

## Writing

### Links

```
# https://github.com/hexojs/hexo/wiki/Breaking-Changes-in-Hexo-3.0

{% post_path hello-world %}
// /2015/01/16/hello-world/
{% post_link hello-world %}
// <a href="/2015/01/16/hello-world/">Hello World</a>
{% post_link hello-world Custom Title %}
// <a href="/2015/01/16/hello-world/">Custom Title</a>

{% asset_path example.jpg %}
// /2015/01/16/hello-world/example.jpg
{% asset_link example.jpg %}
// <a href="/2015/01/16/hello-world/example.jpg">example.jpg</a>
{% asset_link example.jpg Example %}
// <a href="/2015/01/16/hello-world/example.jpg">Example</a>
{% asset_img slug %}
// <img src="/2015/01/16/hello-world/example.jpg">
```

Reference other posts: `{% post_link how-to-reference-package-variables %}`
