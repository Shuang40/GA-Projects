# ![](https://ga-dash.s3.amazonaws.com/production/assets/logo-9f88ae6c9c3871690e33280fcf557f33.png) # Capstone - Resumes and Job Ads Recommender

### Overview

#### Problem Statement

HR practitioners and/or hiring managers could have been spending too much time trying to sieve through many resumes for shortlisting suitable candidates whom they can contact for interview.
As a job seeker, we may also find ourselves spending so much times looking through plentiful job advertisements which may not be relevant to us.
Wouldn't it be nice if pre-selection can be done which will effectively save time for all of us?

We will be using Natural Language Processing and Recommender System to group similar job seekers / job advertisements.
Success will be evaluated by the (TBD on model) to match the job the grouped job seekers to the most suitable job advertisements and vice versa.

---

### Executive Summary

#### Business Overview and Process

We scrape the website spiderjob.com for resumes. In view that api key is not available, we used BeautifulSoup and regex to get the desired information. As we encounter roadblock on the time connection timeout despite introducing bot agent, we limit the job categories to Accounting and Information Technology for this capstone. 
For the job ads, we based it on existing dataset that is available on Kaggle which was used for predicting fake job posting since the features in this dataset has 80% simiarity to the resumes dataset.

As we were cleaning the resumes dataset, we at the same made decision which features will be important for us to have and which are the one to drop. In view that job title, objective, experience and skills are free texts that hold meaningful words for our analysis, we create a new feature and combine all where we then split the text into words, return them to their root form and also remove the stop words. These are performed for the job ads dataset.

TBC.....

---
### Datasets

#### Datasets saved and created

- [Accounting](./datasets/Accounting.csv)
- [IT](./datasets/IT.csv)
- [job_posting](./datasets/job_posting.csv)
- [resumes](./datasets/resumes.csv)

#### Datasets researched and used for scraping

https://www.jobspider.com/ \
https://www.kaggle.com/shivamb/real-or-fake-fake-jobposting-prediction


#### Data Dictionary

| Data Dictionary for resume 	|                                                                                           	|
|:----------------------------	|:-------------------------------------------------------------------------------------------	|
| date_posted                	| the date resume was posted                                                                	|
| job_title                  	| job title that the candidate is looking for                                               	|
| industry                   	| job industry                                                                              	|
| state                      	| postal abbreviation of the state name                                                     	|
| state_name                 	| name of the US state                                                                      	|
| resume_href                	| link to join with url for entering resume                                                 	|
| id                         	| candidate respective resume id                                                            	|
| emp_type                   	| employment type e.g. full time/part time permanent, contract, etc                         	|
| availability               	| date that the candidate is available                                                      	|
| desired_wage               	| desired wage of the candidate                                                             	|
| work_auth                  	| if the candidate is authorized to work in USA                                             	|
| job_level                  	| job level of the candidate e.g. Executive (President, VP, CEO), new grad/entry level, etc 	|
| will_travel                	| if candidate is willing to travel if job is required                                      	|
| edu_level                  	| candidates' education level e.g. bachelors, masters, etc                                  	|
| will_reloc                 	| if candidate is willing to relocation if job is required                                  	|
| objective                  	| candidate's written objective, similar to an executive summary                            	|
| exp                        	| candidate's experience of past jobs                                                       	|
| edu                        	| candidate's education in details                                                          	|
| skills                     	| candidate's skills e.g. Microsoft office, financial reporting, etc                        	|
| add_info                   	| additional information of the candidates                                                  	|

---

### Summary

#### Web Scraping

Due to timeout error, we are unable to scraped all jobs from the website, thus, we ended up with 2 job categories for resumes namely Accounting and Information Technology.

#### Data cleaning / EDA

As we were cleaning the resumes dataset, we at the same made decision which features will be important for us to have and which are the one to drop. In view that job title, objective, experience and skills are free texts that hold meaningful words for our analysis, we create a new feature and combine all where we then split the text into words, return them to their root form and also remove the stop words. These are performed for the job ads dataset.

A quick look of the wordcloud visualization:

Wordcloud for Accounting resumes\
![picture](images/wordcloud_accounting.png)

Wordcloud for Information Technology resumes\
![picture](images/wordcloud_it.png)


#### Data Modeling and Evaluation


### Conclusion and Recommendations


#### Summary and conclusion:

