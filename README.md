# A/B Testing project


## Introduction 

This academic project is based on an actual experiment that was run by Udacity. The specific numbers have been changed, but the patterns have not. Here I flesh the experiment idea out into a fully defined design, analyze the results, and propose a high-level follow-on experiment. 

At the time of this experiment, Udacity courses had two options on the home page: "start free trial", and "access course materials". If the student clicked "start free trial", they would be asked to enter their credit card information, and then they would be enrolled in a free trial for the paid version of the course. After 14 days, they would automatically be charged unless they canceled first. If the student clicked "access course materials", they would be able to view the videos and take the quizzes for free, but they would not receive coaching support or a verified certificate, and they would not submit their final project for feedback.

In the experiment, Udacity tested a change where if the student clicked "start free trial", they were asked how much time they had available to devote to the course. If the student indicated 5 or more hours per week, they would be taken through the checkout process as usual. If they indicated fewer than 5 hours per week, a message would appear indicating that Udacity courses usually require a greater time commitment for successful completion, and suggesting that the student might like to access the course materials for free. At this point, the student would have the option to continue enrolling in the free trial, or access the course materials for free instead. This screenshot shows what the experiment looks like.

The hypothesis was that this might set clearer expectations for students upfront, thus reducing the number of frustrated students who left the free trial because they didn't have enough time—without significantly reducing the number of students to continue past the free trial and eventually complete the course. If this hypothesis held true, Udacity could improve the overall student experience and improve coaches' capacity to support students who are likely to complete the course.

The unit of diversion was a cookie, although if the student enrolled in the free trial, they were tracked by user-id from that point forward. The same user-id could not enroll in the free trial twice. For users that did not enroll, their user-id was not tracked in the experiment, even if they were signed in when they visited the course overview page.


## Experiment Design

### Metric Choice

**Invariant metrics**

I chose the following ones:

* Number of cookies: that is the number of unique cookies to view the course overview page. Setting this metric as an invariant allows us to check if assignment to control vs. experimental groups functions properly, in broad terms and allows us to detect any underlying issues which might potentially affect the integrity of the experiment. 
* Number of clicks: this is the number of unique cookies to click the “start free trial” button. Setting this metric as an invariant allows us to create an “even base” for doing a meaningful comparison of the proportion of those who take subsequent steps, such as enroll and / or pay through after the 14-day period. 

I did NOT choose the following ones:

* Number of user IDs, that is, number of users who enroll in the free trial. The reason I did not choose this as an invariant is that we would actually expect this metric to vary if the warning message about required hours of week is effective. 

**Evaluation metrics**

I chose the following ones:

* Gross conversion: it makes perfect sense to measure the number of user IDs to complete checkout and enroll in the free trial divided by unique cookies to click the “Start free trial” button. The warning message is supposed to make a person think twice about the time commitment before enrolling. Thus, a certain number of people, who are not sure they can commit sufficient time, would conceivably decide not to enroll. Therefore, one would expect gross conversion to be lower in the experiment group compared to the control group. 
* Retention: if the warning message is effective and only the people who are well-informed about required time commitment and are prepared to give this time commitment enroll in the course, the probability of their paying through the 14-day period will be higher. Hence, retention, measured as the number of user IDs to remain enrolled past the 14-day boundary divided by the number of user IDs to complete the checkout is expected to increase in the experiment group. 
* Net conversion: this is the number of user IDs to remain enrolled past the 14-day boundary divided by the number of unique cookies to click the “Start free trial” button. Interestingly, this measure can be decomposed as follows: (Pay/Enroll) x (Enroll/Click). This effectively means that Net Conversion is a product of Gross Conversion and Retention. As we expect Gross Conversion to go down, while Retention to go up, the two effects may offset each other and Net Conversion may not change. However, it is interesting to verify this and I decided to include as an evaluation metric. 

I did NOT choose the following ones:

* Click-through probability: I did not include this as an evaluation metric, as the objective of this experiment is not about the probability of a page viewer’s clicking the “Start free trial” button. Rather, the study is concerned with the process after one clicks the “Start free trial” button. 



### Measuring Standard Deviation

Given the baseline values (first tab in the Excel file), I calculated analytic estimates of the standard deviations of the evaluation metrics, given the sample size of 5,000 cookies visiting the course overview page. I believe analytic estimates would approximate empirical estimates well, as (i) we are dealing with probability metrics, hence it is reasonable to assume a binomial distribution and (ii) we have a large number of instances, which should make binomial distribution close to normal. The only metric where we may want to check the standard deviation empirically is retention, as here the total number of instances (82 enrollments on the basis of 5,000 page visits) is rather low. 

* Gross conversion    SD = 0.0202 
* Retention           SD = 0.0549
* Net conversion      SD = 0.0156

### Sizing

**Number of Samples vs. Power**

Given the baseline values and the alpha and beta values of 0.05 and 0.2, respectively, I used the online calculator to estimate the required number of pageviews for each of the three metrics. Based on the highest one, I concluded that we will need 4,740,000 pageviews (later to be split between control and experiment) to power the experiment appropriately. 

I will not use Bonferroni correction because the metrics are inter-related and Bonferroni may lead to overly conservative conclusions. 

Duration vs. Exposure

I would direct 100% of my traffic through this experiment, as I don’t see the risk exposure to be very high by incorporating the message clarifying the required time commitment. 

On this basis, it will take 119 days to run the experiment. 


## Experiment Analysis

### Sanity Checks

For my invariant metrics, below are the 95% confidence interval for the value I expect to observe, along with the actual observed value. Thus, both metrics passes the sanity check. 

* Number of cookies   0.4988 - 0.5012, observed: 0.5006
* Number of clicks  0.4959 - 0.5041, observed: 0.5005

### Result Analysis

**Effect Size Tests**

Below are the effect size tests and corresponding conclusions for each of the three evaluation metrics. As expected, Gross Conversion decreased and Retention increased. The former is both statistically and practically significant, while the latter – only statistically significant. Net conversion is not significant at all. As suspected the opposite effects of Gross Conversion and Retention seem to have offset each other. As explained in a previous section, I did not use Bonferroni correction. 

* Gross conversion -0.0291 to -0.0120 both statistically and practically significant 
* Retention 0.0081 to 0.0541 statistically but NOT practically significant 
* Net conversion -0.0116 to 0.0019 NEITHER statistically NOR practically significant 

**Sign Tests**

I additionally performed sign tests, the results of which are below. 

* Gross conversion 0.0026 statistically significant  
* Retention 0.6776 NOT statistically significant
* Net conversion 0.6776 NOT statistically significant 

There is a discrepancy between the effect size test and sign test in the case of Retention: effect size is significant, while sign test – is not. We can find an explanation if we look at the daily differences. The magnitudes of daily positive differences are consistently higher than the negative ones. The effect size captures magnitudes, while the sign test does not. 

 
### Recommendation

Given the fact that Gross Conversion is practically significant, I recommend launching the change. The meaningful reduction in Gross Conversion means that there will be fewer frustrated students, which is good for the brand. The potential reduction of revenue due to the lower gross conversion is offset by the increase in retention. The fact that there is no significant change in Net Conversion, proves that overall revenues should not suffer due to this change. Therefore, the effect of this change can be summarized as “increased student satisfaction without any loss of revenue”. To me, this makes a compelling case for launching the change. 

## Follow-Up Experiment

Give a high-level description of the follow up experiment you would run, what your hypothesis would be, what metrics you would want to measure, what your unit of diversion would be, and your reasoning for these choices.

