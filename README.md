# Running Jekyll

## Requirements

**Install Ruby**

Ubuntu
```
sudo apt-get install ruby-full
```

**Install Jekyll**

Installing jekyll requires GCC and Make to be installed. These come preinstalled on Ubuntu.

```
sudo gem install jekyll
```

**Install required gems**

Install required gems listed in `_config.yml`

```
sudo gem install jekyll-paginate jekyll-seo-tag jekyll-sitemap
```

**Run site locally**

Run the site locally...

```
jekyll serve
```
