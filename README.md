TODO Steps and any changes I did


# New posts

```bash
hexo new draft TITLE


# Once ready to post
hexo publish FILENAME
```

# Install

Docker: See [docker-hexo](https://github.com/martindsouza/docker-hexo)


Old: 

```bash
npm install hexo-deployer-git --save
npm install hexo-browsersync --save
npm install -g hexo-cli
```

# Testing

`hexo server --draft`

# Build

```bash
git clone TODO ssh github
npm install
```

## Hexo Specific

```bash
# Clean the database
hexo clean

# Generate content
hexo generate

# Deployment (using hexo-deployer-git)
hexo deploy -g



# Manual Deployment
# Do this once:
git clone --single-branch --branch gh-pages git@github.com:martindsouza/talkapex.git .deploy_git

# Deploy

# Deploy (via Docker CLI)
hexo clean
hexo generate
rm -rf .deploy_git/*
cp -r public/* .deploy_git/
cd .deploy_git
git add *
git commit -m "Site updated"
git push
cd ..

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
{% asset_img example.png %}
// <img src="/2015/01/16/hello-world/example.jpg">
```

Reference other posts: `{% post_link how-to-reference-package-variables %}`
