---
layout: post
title: Creating a Resume Builder
permalink: posts/resume_builder
author: Daniel Huang
---

## About
This is a webapp based project that helps to automate a resume based on information that you provide. 
The webapp also provides job recommendations based on Indeed. Linked is the [git repository](https://github.com/EGZJ17/pic16b-mnist-demo)!

## Overview 

UNDERSTANDING A RESUME BUILDER 

It is a webapp developed to simplify the task of creating a resume for individuals, providing effective means of designing a professional looking resume. The webapp is especially designed for individuals who have a hard time creating their resume such as graduate students. This way, they will get a clear idea of the sections and information that should be included in a resume. The system is pretty simple without too much flexibility but can be expanded to include different templates, sections and so on. This project is user-friendly and requires minimum human intervention. Individuals just have to fill up a form that specifies questions from all required fields such as personal questions, educational, experience and skills. The answers provided by the users are stored and the system automatically generates a well structured resume.

OVERVIEW OF RECOMMENDATION SYSTEM 

The webapp also provides a site to input the preferred job title and location in order to generate the top job postings on [Indeed](https://www.indeed.com/). This way, the user can immediately start applying to job right after building their resume. 

The webapp is built on three main components: 
- An Indeed Web Scraper
- Flask Webapp
- HTML and CSS Files.

SOFTWARE REQUIREMENTS:
- Programming Language: Python
- User Interface: HTML and CSS

Below is an example of an example resume that was created using our webapp!
![bruindoe_resume.png]({{ site.baseurl }}/images/bruindoe_resume.png)

### Component 1: Indeed Scraper 
As an extension of the resume builder, we wanted to generate job recommendations for the user based on their desired job and location. Currently, our code is able to take the user's input and execute a search on indeed.com and return the corresponding search results from the site in the form of a table. Users are able to see information about the job posting, including the job title, a brief job description, as well as the link to the job posting itself. 
We initially intended to scrape job postings from LinkedIn, but eventually landed on Indeed.com due to challenges with bypassing LinkedIn's login requirements. Our code makes use of Beautiful Soup library to scrape the job postings, and the web driver from selenium to test out our automated search. By using Beautiful Soup functions such as select(),find(),get_text() in tandem with CSS selectors, the program extracts the job title, the company name, the company location, the date of posting, the job description, and the link to the job posting from the first 10 results of the search. The information would then be loaded into a table formatted in HTML and displayed on the same page.

In order to handle the user's input and make the table output readable, we did a number of string manipulations within our function. Inside url's, spaces are formatted as '%20'. In order for our scraper to access the right website, with the correct job and location requested by the user we made sure that the formatting inside the current_url would be similar to if someone were to access indeed on their own. Afterwards we would load the html content from that website and loop through each job posting, which we could select with our bs4 css selections (e.g. content.select('.job_seen_beacon'). Using lists, we can store and index each aspect of the job posting. Like with the user_job and user_location, we applied string manipulations to some of the elements scraped from indeed such that our job postings are more readable for the user. We then pass all of these variables back to the 'indeed_test.html', and the html would render the values into a neat table for the user to access.

```python
def submit():
    if request.method == 'GET':
        return render_template('indeed_test.html')
    else:
        try:
            user_job = request.form['job']
            user_location = request.form['location']
            user_job_n = user_job.replace(" ", "%20")
            user_location_n = user_location.replace(" ", "%20")
            current_url = f"https://www.indeed.com/jobs?q={user_job_n}&l={user_location_n}"

            #resp = requests.get(current_url)
            content = BeautifulSoup(requests.get(current_url, headers=HEADERS).content, 'lxml')
            jobs_list = []    
            jobs_fixedlist = []
            company_list = []
            location_list = []
            date_list = []
            date_fixedlist = []
            job_desc_list = []
            job_link_list = []
            job_fixedlink_list = []
            for post in content.select('.job_seen_beacon'):
                jobs_list.append(post.select('.jobTitle')[0].get_text().strip()),
                company_list.append(post.select('.companyName')[0].get_text().strip()),
                location_list.append(post.select('.companyLocation')[0].get_text().strip()),
                date_list.append(post.select('.date')[0].get_text().strip()),
                job_desc_list.append(post.select('div.job-snippet')[0].get_text().strip()),
                job_link_list.append(post.find(class_='jcs-JobTitle', href=True)['href'])

            for date in date_list:
                date_fixedlist.append(date.replace('Posted', '').replace('EmployerActive', ''))

            for job_title in jobs_list:
                jobs_fixedlist.append(job_title.replace('new', ''))

            for links in job_link_list:
                job_fixedlink_list.append('https://www.indeed.com' + links)


            return render_template('indeed_test.html', 
            user_job=user_job, user_location=user_location, 
            job1 = jobs_fixedlist[0], job2 = jobs_fixedlist[1], job3 = jobs_fixedlist[2], job4 = jobs_fixedlist[3], job5 = jobs_fixedlist[4], 
            job6 = jobs_fixedlist[5], job7 = jobs_fixedlist[6], job8 = jobs_fixedlist[7], job9 = jobs_fixedlist[8], job10 = jobs_fixedlist[9], 
            company1 = company_list[0], company2 = company_list[1], company3 = company_list[2], company4 = company_list[3], company5 = company_list[4],
            company6 = company_list[5], company7 = company_list[6], company8 = company_list[7], company9 = company_list[8], company10 = company_list[9],
            loc1 = location_list[0], loc2 = location_list[1], loc3 = location_list[2], loc4 = location_list[3], loc5 = location_list[4],
            loc6 = location_list[5], loc7 = location_list[6], loc8 = location_list[7], loc9 = location_list[8], loc10 = location_list[9],
            date1 = date_fixedlist[0], date2 = date_fixedlist[1], date3 = date_fixedlist[2], date4 = date_fixedlist[3], date5 = date_fixedlist[4],
            date6 = date_fixedlist[5], date7 = date_fixedlist[6], date8 = date_fixedlist[7], date9 = date_fixedlist[8], date10 = date_fixedlist[9],
            desc1 = job_desc_list[0], desc2 = job_desc_list[1], desc3 = job_desc_list[2], desc4 = job_desc_list[3], desc5 = job_desc_list[4],
            desc6 = job_desc_list[5], desc7 = job_desc_list[6], desc8 = job_desc_list[7], desc9 = job_desc_list[8], desc10 = job_desc_list[9],
            link1 = job_fixedlink_list[0], link2 = job_fixedlink_list[1], link3 = job_fixedlink_list[2], link4 = job_fixedlink_list[3], link5 = job_fixedlink_list[4],
            link6 = job_fixedlink_list[5], link7 = job_fixedlink_list[6], link8 = job_fixedlink_list[7], link9 = job_fixedlink_list[8], link10 = job_fixedlink_list[9],
            current_url=current_url)
            
        except:
            return render_template('indeed_test.html', error = True)
```

This is an example of the results of parsing through indeed on our webapp.
![indeed_scraper.png]({{ site.baseurl }}/images/indeed_scraper.png)