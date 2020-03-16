---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "NBA News"
summary: "a website will automatically update NBA news"
authors: []
tags: []
categories: []
date: 2020-03-16T15:18:45+08:00

# Optional external URL for project (replaces project detail page).
external_link: ""

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: "Top"
  preview_only: false

# Custom links (optional).
#   Uncomment and edit lines below to show custom links.
# links:
# - name: Follow
#   url: https://twitter.com
#   icon_pack: fab
#   icon: twitter

url_code: ""
url_pdf: ""
url_slides: ""
url_video: ""

# Slides (optional).
#   Associate this project with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides = "example-slides"` references `content/slides/example-slides.md`.
#   Otherwise, set `slides = ""`.
slides: ""
---
#### This project is based on two-part. 
First, the api is developed in Django Rest Framework. And also it will crawl the latest NBA news from another website every day. The second part is Frontend, it’s using React and deploy on github page.


[demo link](https://zooyo.github.io/nbapages/)


- Backend was deployed on heroku, so it need 30 seconds to wake server based on free project.
- Also, since to save usage of cloud service. Automatically crawler has been turn off.