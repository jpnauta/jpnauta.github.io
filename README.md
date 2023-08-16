> Github Pages for Jeremy Nauta

Adapted from [Hugo Minimal Theme](https://themes.gohugo.io/minimal/)

# Installation

This site uses [Hugo][hugo] to generate the site.

```bash
brew install hugo
```

# Development

Use this to run the development server.

```bash
hugo server -D
```

# Adding Content

Use this command to generate a new page

```bash
hugo new posts/my-post.md
```

# Publishing


If you haven't already, you will need to add
[jpnauta.github.io][jpnauta.github.io repo] as a Git Submodule.

```bash
git submodule add -b master git@github.com:jpnauta/jpnauta.github.io.git public
```

To publish, simply run the bash command.

```bash
bash deploy.sh
```


[jpnauta.github.io repo]: https://github.com/jpnauta/jpnauta.github.io
[hugo]: https://gohugo.io/