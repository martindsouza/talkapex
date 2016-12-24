WIP

Changes I made:

* https://github.com/hexojs/hexo/issues/1971
  * Modify `hexo-generator-index/lib/generator.js`
    * Change `return pagination('', posts, {` to `return pagination('blog', posts, {`
  * Modfy `themes/<theme_name>/config.yml`

```
menu:
  Home: /
  Blog: /blog
  Archives: /archives
```
