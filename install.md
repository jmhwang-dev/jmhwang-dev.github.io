# Jekyll + GitHub Pages Setup Guide

This guide will walk you through setting up **Jekyll** to work with **GitHub Pages** using `rbenv` and the `github-pages` gem.

---

## Prerequisites

Make sure you have the following tools installed:

* Git
* Homebrew (macOS)
* `rbenv` (Ruby version manager)
* Ruby 3.1 or higher

---

## 1. Install Ruby with rbenv

### Install rbenv and ruby-build

```bash
brew install rbenv ruby-build
```

### Initialize rbenv in your shell

**For zsh (`~/.zshrc`)**:

```bash
export PATH="$HOME/.rbenv/bin:$PATH"
eval "$(rbenv init - zsh)"
```

**For bash (`~/.bash_profile` or `~/.bashrc`)**:

```bash
export PATH="$HOME/.rbenv/bin:$PATH"
eval "$(rbenv init -)"
```

Then restart your terminal or run:

```bash
source ~/.zshrc  # or ~/.bash_profile
```

### Install Ruby

```bash
rbenv install 3.1.4
rbenv global 3.1.4
rbenv rehash
```

### Verify installation

```bash
ruby -v
# Should show: ruby 3.1.4p...
```

---

## 2. Install Bundler

```bash
gem install bundler
```

---

## 3. Create a New Jekyll Site

```bash
gem install jekyll
jekyll new my-awesome-site
cd my-awesome-site
```

---

## 4. Configure for GitHub Pages

> Follow this section If you create first pages

Edit the `Gemfile`:

* Replace:

```ruby
gem "jekyll"
```

* With:

```ruby
gem "github-pages", group: :jekyll_plugins
```


## 5. Install

```bash
bundle install
```

---

## 6. Serve Locally

```bash
bundle exec jekyll serve
```

Visit `http://localhost:4000` to preview your site.


## Resources

* [Jekyll Docs](https://jekyllrb.com/docs/)
* \[GitHub Pages Docs]\([https://docs.github.com/en/](https://docs.github.com/en/)
