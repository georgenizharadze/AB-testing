# A/B Testing project


## Introduction 

This academic project is based on an actual experiment that was run by Udacity. The specific numbers have been changed, but the patterns have not. Here I flesh the experiment idea out into a fully defined design, analyze the results, and propose a high-level follow-on experiment. 

At the time of this experiment, Udacity courses had two options on the home page: "start free trial", and "access course materials". If the student clicked "start free trial", they would be asked to enter their credit card information, and then they would be enrolled in a free trial for the paid version of the course. After 14 days, they would automatically be charged unless they canceled first. If the student clicked "access course materials", they would be able to view the videos and take the quizzes for free, but they would not receive coaching support or a verified certificate, and they would not submit their final project for feedback.

In the experiment, Udacity tested a change where if the student clicked "start free trial", they were asked how much time they had available to devote to the course. If the student indicated 5 or more hours per week, they would be taken through the checkout process as usual. If they indicated fewer than 5 hours per week, a message would appear indicating that Udacity courses usually require a greater time commitment for successful completion, and suggesting that the student might like to access the course materials for free. At this point, the student would have the option to continue enrolling in the free trial, or access the course materials for free instead. 

The hypothesis was that this might set clearer expectations for students upfront, thus reducing the number of frustrated students who left the free trial because they didn't have enough time—without significantly reducing the number of students to continue past the free trial and eventually complete the course. If this hypothesis held true, Udacity could improve the overall student experience and improve coaches' capacity to support students who are likely to complete the course.

The unit of diversion was a cookie, although if the student enrolled in the free trial, they were tracked by user-id from that point forward. The same user-id could not enroll in the free trial twice. For users that did not enroll, their user-id was not tracked in the experiment, even if they were signed in when they visited the course overview page.

Thus, in this document I act as if I am in charge of the experiment and I design it, as well as analyze a dataset provided by Udacity, to draw conclusions and make recommendations. 


## Experiment Design

### Metric Choice

**Invariant metrics**

I chose the following ones:

* Number of cookies: this is the number of unique cookies to view the course overview page. Setting this metric as an invariant allows us to check if assignment to control vs. experimental groups functions properly, in broad terms and allows us to detect any underlying issues which might potentially affect the integrity of the experiment. 
* Number of clicks: this is the number of unique cookies to click the “start free trial” button. Setting this metric as an invariant allows us to create an “even base” for doing a meaningful comparison of the proportion of those who take subsequent steps, such as enroll and / or pay through after the 14-day period. 

I did NOT choose the following ones:

* Number of user IDs, that is, number of users who enroll in the free trial. The reason I did not choose this as an invariant is that we would actually expect this metric to vary if the warning message about required hours of week is effective. 

* Click-through probability: this is practically the ratio of the number of unique cookies to click and the number of unique cookies to view the course page, both of which are already set as invariant metrics. Therefore, it is redundant to measure click-through probability. 

**Evaluation metrics**

I chose the following metrics: 

* Gross conversion: this metric should decrease significantly to justify launching the change. 
* Retention: this metric should increase significantly to justify launching the change. 
* Net conversion: this metric should NOT decrease significantly to justify launching the change. A decrease in this metric would indicate potential loss of revenues. 

Practical significance levels for the above three metrics have been set at 0.01, 0.01 and 0.0075, respectively. 

Below is a more in-depth discussion on these metrics and their expected behavior. 

* Gross conversion: it makes perfect sense to measure the number of user IDs to complete checkout and enroll in the free trial divided by unique cookies to click the “Start free trial” button. The time commitment warning message is supposed to make a person think twice about time commitment before enrolling. Thus, a certain number of people, who are not sure they can commit sufficient time, would conceivably decide not to enroll. Therefore, one would expect gross conversion to be lower in the experiment group compared to the control group. 
* Retention: if the warning message is effective and only the people who are well-informed about required time commitment and are prepared to give this time commitment enroll in the course, the probability of their paying through the 14-day period will be higher. Hence, retention, measured as the number of user IDs to remain enrolled past the 14-day boundary divided by the number of user IDs to complete the checkout is expected to increase in the experiment group. 
* Net conversion: this is the number of user IDs to remain enrolled past the 14-day boundary divided by the number of unique cookies to click the “Start free trial” button. Interestingly, this measure can be decomposed as follows: (Pay/Enroll) x (Enroll/Click). This effectively means that Net Conversion is a product of Gross Conversion and Retention. As we expect Gross Conversion to go down, while Retention to go up, the two effects may offset each other and Net Conversion may not change. However, it will be interesting to verify this and I decided to include as an evaluation metric. 


### Measuring Standard Deviation

Given the baseline values (first tab in the Excel file), I calculated analytic estimates of the standard deviations of the evaluation metrics, given the sample size of 5,000 cookies visiting the course overview page. I believe analytic estimates would approximate empirical estimates well, as (i) we are dealing with probability metrics, hence it is reasonable to assume a binomial distribution and (ii) we have a large number of instances, which should make binomial distribution close to normal. Additionally, to make a judgement about the adequacy of analytic estimates, I check if the unit of analysis and unit of diversion are the same for the metrics. For gross and net conversions, the unit of analysis is "clicks", defined as the number of **unique cookies** to click the "Start free trial" button. As the unit of diversion is cookies, the unit of analysis and unit of diversion do coincide. For retention, the unit of analysis is user ID. We know that after enrollment, user ID becomes the unit of diversion. Therefore, the unit of analysis and unit of diversion do coincide in this case as well. 

Based on the above reasoning, I conclude that the analytic estimates of standard deviations should be good approximations of the real underlying values. 

* Gross conversion    SD = 0.0202 
* Retention           SD = 0.0549
* Net conversion      SD = 0.0156

### Sizing

**Number of Samples vs. Power**

Given the baseline values and the alpha and beta values of 0.05 and 0.2, respectively, I used the [online calculator](http://www.evanmiller.org/ab-testing/sample-size.html) to estimate the required number of pageviews for each of the three metrics. Based on the highest value associated with the three metrics, a total of 4,740,000 pageviews will be required to power the experiment appropriately. Even if we route the entire traffic through the experiment, this is equivalent to 119 days, which is prohibitively long. This duration is driven by the retention metric. The other two metrics, gross and net conversion, require durations multiples lower. Therefore, I recommend dropping Retention altogether. I believe a sound business decision can be made on the basis of the gross and net conversion metrics only. These require 646K and 686K pageviews, respectively. Based on the higher number, the experiment will only require 18 days, if the entire traffic is routed through it. 

**Duration vs. Exposure**

I don’t see this experiment carrying a high risk exposure. The inclusion of the message clarifying the required time commitment (i) cannot really cause any harm to anybody, (ii) does not involve any data privacy or confidentiality issues and (iii) it is difficult to imagine that it will cause any significant frustration to prospective users and thus damage the brand somehow. 

On this basis, I recommend taking 100% of the traffic through the experiment, which will then take 18 days to complete. 


## Experiment Analysis

### Sanity Checks

For my invariant metrics, below are the 95% confidence interval for the value I expect to observe, along with the actual observed value. Thus, both metrics pass the sanity check. 

* Number of cookies   0.4988 - 0.5012, observed: 0.5006
* Number of clicks  0.4959 - 0.5041, observed: 0.5005

### Results Analysis

**Effect Size Tests**

Below are the effect size tests and corresponding conclusions for each of the evaluation metrics. As expected, Gross Conversion decreased. The change is both statistically and practically significant. The change in net conversion is not significant at all. 

* Gross conversion: -0.0291 to -0.0120, both statistically and practically significant 
* Net conversion: -0.0116 to 0.0019, NEITHER statistically NOR practically significant 

**Sign Tests**

I additionally performed sign tests, the results of which are below. 

* Gross conversion: 0.0026, statistically significant  
* Net conversion: 0.6776, NOT statistically significant 

As we can see, the sign tests bear out the effect size tests.  

I did not use Bonferroni correction in my analysis, as we are dealing with a situation where multiple (two, in our case) metrics have to meet our criteria **at the same time** for us to launch the change. As we know, Bonferroni correction uses significantly reduced statistical significance levels and is hence very conservative. This increases the chance of rejection of one of the multiple metrics, which will in turn mean the rejection of the entire proposal. In other words, the probability of Type II error goes up. Bonferroni correction is more appropriate in situations where we have multiple metrics and when the confirmation of **any** of them is sufficient for the launch. In this setting, it is justified to be conservative to reduce the probability f Type I error. 


### Recommendation

The analysis of the results shows that:

* Gross conversion did decrease in a practically significant manner, which meets the launch decision criterion set out at the beginning. 
* Net conversion did NOT decrease in a statistically significant manner, which also meets our launch decision criterion. 

Based on the above, I recommend launching the change. The meaningful reduction in Gross Conversion means that there will be fewer frustrated students, which is good for the brand. The fact that there is no significant change in Net Conversion proves that overall revenues should not suffer due to this change. Therefore, the effect of this change can be summarized as “increased student satisfaction without any loss of revenue”. To me, this makes a convincing case for launching the change. 

As a caveat, it should be noted that the lower confidence level boundary of the change in net conversion (-0.0116) lies below the practical significance level of -0.0075. This indicates certain probability (c. 30%) that net conversion may have actually decreased beyond the practical significance level. Therefore, while the test outcome does meet the launch criteria and the change can be launched, if the business wants to obtain an even higher confidence, an additional experiment can be carried out with a higher test power. 


## Follow-Up Experiment

To do even more towards increasing the student's chances of success, I would introduce a basic prerequisite skills test prior to enrollment. The hypothesis is that this will allow the student to get an objective assessment of their skills and gauge even better their chances of completing the course successfully. The experiment will follow the same lines as the one described above. If the student clicks "start free trial", they will be asked to take the skills test. If the student passes the test, they will be taken through the checkout process as usual. Otherwise, a message will appear suggesting they upgrade their skills (e.g. basic python coding, prob and stats knowledge, etc.) and come back, or suggesting that the student might like to access the course materials for free. At this point, the student will have the option to continue enrolling in the free trial, or access the course materials or leave and come back later. 

The unit of diversion will be a cookie and after enrollment - user ID. Invariant metrics will be the same - the number of cookies to view the page and number of clicks on "Start free trial" button. Evaluation metrics will be gross and net conversion, as defined in previous experiment. 

This experiment does not carry any risk in terms of bringing potential harm to the student, nor does in involve any data privacy and confidentiality issues. However, the proposed test can be somewhat cumbersome and there is a risk that prospective students may find it off-putting. Therefore, I propose not to take the entire traffic through the experiment but rather, only 50%. This will still allow completing the experiment in about 5 weeks. 