+++
# Experience widget.
widget = "experience"  # See https://sourcethemes.com/academic/docs/page-builder/
headless = true  # This file represents a page section.
active = true  # Activate this widget? true/false
weight = 40  # Order that this section will appear.

title = "Experience"
subtitle = ""

# Date format for experience
#   Refer to https://sourcethemes.com/academic/docs/customization/#date-format
date_format = "Jan 2006"

# Experiences.
#   Add/remove as many `[[experience]]` blocks below as you like.
#   Required fields are `title`, `company`, and `date_start`.
#   Leave `date_end` empty if it's your current employer.
#   Begin/end multi-line descriptions with 3 quotes `"""`.
[[experience]]
  title = "Backend Developer - Python"
  company = "MaideaX"
  company_url = "https://www.maideax.com/"
  location = "Taiwan, New Taipei"
  date_start = "2020-03-01"
  date_end = "2022-03-01"
  description = """
  Responsibilities include:
  
  * Third-party payment related website development
  * Develop sandbox(mock third-party payment) for internal usage
  * Deploy production server on EC2 using Docker
  * Writing bash script to initialize EC2(Ubuntu) environment for Docker deployment
  * Minimize downtime of production server while changing Docker image
  * Fetch and parse specific content in banks SMS
  * Automation process for bank App on phone using uiautomator2
  * Text recognition for simple captcha using OpenCV, tesseract
  * Improve text recognition accuracy using a pre-trained model
  * Refator structure from DRF + jQuery to GraphQL + React
  """

[[experience]]
  title = "Software Engineer Intern"
  company = "WPG Holdings"
  company_url = "https://www.wpgholdings.com/"
  location = "Taiwan, Taipei"
  date_start = "2019-02-01"
  date_end = "2019-07-01"
    description = """
  Responsibilities include:

  * Wrote automation tests with Katalon and test Restful API using Postman
  * Maintained DADATONG and developed new functions. For example Announcement
  * Designed spring boot API work with SQL server which can manipulate CRUD
  * Implemented translate program for addresses in excel file with google API in Python
  * Developed Slack bot which can call basic API such as return image by keyword
  """

  

+++
