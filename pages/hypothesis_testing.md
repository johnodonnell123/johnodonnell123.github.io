# The Statistics Behind A/B Testing

## What is a Statistical Test?
Statistical testing is a tool used to determine if there is a statistically significant difference between two groups. If statistically significant doesn't mean anything to you, it will soon!
Companies are always trying to improve through various initiatives/projects. At times it can be hard to quantify their impact, particularly if it is small. For example, if churn rate decreases by 0.5% after launching a new product feature, how can we confidently say that this change is the result the feature? Proper A/B testing helps us sort this out.
For a better intuition on how improving churn by 0.5% can be meaningful, read here.

### Overview
All the concepts required for A/B testing will be covered in this article. Below is a general outline of topics and their relevance.
1. **The Normal Distribution:** allows us to relate standard deviations to probabilities
2. **The Sampling Distribution and Standard Error:** helps create distributions for testing sample means instead of single data points
3. **The Distribution of Differences:** helps create distributions for testing differences between samples
4. **The T-Distribution:** Helps us quantify uncertainty driven by smaller sample sizes
5. **Bernoulli Trials and the Binomial Distribution:** allows us to create distributions for variables with binary outcome (churn / no churn)

A common theme exists across many statistical tests, we want to find out how many standard deviations some test value is from the mean. That distance is associated with a probability, which determines statistical significance. Larger distances from the mean in standard deviations are most likely to be significant.

### Our Example
Imagine you work for Netflix. Leadership wants to change the homepage layout, and the plan is to show a new version to a random sample of users. We want to see if there is a significant difference in user behavior when exposed to the new page. This may seem like a small tweak, the impact isn't likely to cut churn in half, but what if it decreases churn by 1%? Or maybe increases total viewings by 1%?
So, you are to determine if the new page has a meaningful impact on user behavior. To get there, lets cover some foundational concepts.

### The Normal Distribution:
The normal distribution is a continuous probability distribution, which has a few implications. One being that the area underneath the curve is related to probability, with the total area equal to 1, or 100%. Normal distributions are described by their mean and standard deviation.

<img src="/images/hypothesis_testing/normal_dist.png?raw=true" height = "75%" width = "75%">

### Empirical Rule
Taking this idea further, we arrive at the empirical rule, which helps us relate the area under the curve (probability) to some distance from the mean in standard deviations. The empirical rule states the following:
- ~ 68% of the area falls within ± 1 standard deviation
- ~ 95% within ±2 standard deviations
- ~ 98% within ± 3 standard deviations

<img src="/images/hypothesis_testing/emperical.png?raw=true" height = "75%" width = "75%">

Most of the red area is in the middle, and as we approach the tails there is less. Now we can put numbers to it. Note the distribution is symmetrical around the mean, so we can say that ~34% of the data lives between the mean and +1 standard deviation, or between values 20 and 25.

***Key concept = With the normal distribution, standard deviations have associated probabilities***

We can use the empirical rule to determine how probable values are given a distribution.
- Example 1: The probability of drawing a value from this distribution outside of ±2 standard deviations is ~5%.
- Example 2: To find the probability of drawing values strictly greater +2 standard deviations, we half the previous answer, leaving us with ~2.5%.

### Intuition
Looking at a normal distribution, you can see where most of the values live: the center. Think of this as values closer to the mean being more probable *if they come from this distribution*. A common theme of statistical testing is when given some distribution and a value, we want to know the probability the value does NOT come from the distribution.

Imagine we are given a single data point with a value of 22.5 (blue line below). That is ½ of a single standard deviation. How confidently can we say it was NOT drawn from this distribution? That value is associated with a relatively high probability here, its close to the mean, so confidence is low. But what if that value was 40 (red line). Thats 4 standard deviations away from our mean! We would be more confident that the a value of 40 did ***NOT*** come from this distribution compared to the value of 22.5. Why? Because it's significantly farther from the mean!

<img src="/images/hypothesis_testing/intuitition.png.png?raw=true" height = "75%" width = "75%">

Extend this idea from just a single data point to a sample of size 50. We take 50 samples, and now the mean of those samples is 22.5. With what level of confidence could we say they did NOT come from the above distribution? Confidence may still be pretty low, but intuitively higher than a single point. What if that sample mean was 40? This would provide much more confidence that just a single sample.

The probability of drawing one extreme data point is higher than drawing 50 of them. Larger samples sizes contain less sampling error. The computations for the associated probabilities of single data points and means are a little different, but the concepts are similar. The important ideas here are that distance from the mean (in standard deviations) and our sample size both influence our confidence.

### Z-Scores
The idea of relating standard deviations to probabilities has been covered, but what if a sample doesn't fall right on one of our unit standard deviations in the empirical rule? We need to know how far a given data point is from the mean in standard deviations. For example, if we want to find the probability of drawing a data point ≥ 27 from this distribution , we need to know how many standard deviations 27 is from the mean. This is called a **Z-Score**.

<img src="/images/hypothesis_testing/z_score_dist.png?raw=true" height = "75%" width = "75%">

We find the distance a given point is from the mean, then to convert into units of standard deviation we divide by the standard deviation.

<img src="/images/hypothesis_testing/zscore_equation.png?raw=true" height = "75%" width = "75%">

Plugging in, we return a z-score of 1.4. We now use a z-table to determine the associated probability.

<img src="/images/hypothesis_testing/ztable.png?raw=true" height = "75%" width = "75%">

We return a value of ~92%. By default z-tables tell us the cumulative probability up until a z-score, or the area from the tip of the left tail up to the given score. The question was framed as a "greater than" so we are looking for the area to the right of our score, so we subtract this probability from 1. There is roughly an 8% chance of drawing a score ≥ 27 from this distribution.
Why is this important? We need to understand how to associate probabilities with any distance from the mean.

***Values further from the mean (in standard deviations) are less probable in the distribution, and more likely to be significantly different.***

### Central Limit Theorem and the Sampling Distribution
The central limit theorem states that the sampling distribution of a population will be approximately normal, centered around the true population mean, and have a standard deviation equal to the standard error. Let's unpack that.

A sampling distribution is comprised of many sample means. To create a sampling distribution, we take repeated samples from a population, find the mean of that sample, and create a distribution of those means. This distribution will center around the true population mean.

This distribution will always be approximately normal if our sample size is large enough, regardless of the shape of the population sampled from. This is important because as we have seen, with a normal distributions we can easily relate probabilities to standard deviations.

The sampling distribution will have a standard deviation related to the sample standard deviation, but adjusted for sample size. We call this the Standard Error. When sampling from a population, larger samples are likely to be more representative. Smaller samples have some amount of sampling error, represented as the standard error. The formula for standard error is below.

<img src="/images/hypothesis_testing/se_equation.png?raw=true" height = "75%" width = "75%">

We are dividing the sample standard deviation by the square root of our sample size to arrive at standard error. So, the standard error will always be smaller than the standard deviation.

Why?

Standard error is related to sample means, not individual data points. This calls back to the idea planted earlier: it is less probable to see extreme values when they are means versus if they are single data points. So with standard error being a measure of dispersion, it makes sense it would be smaller than the standard deviation.

<img src="/images/hypothesis_testing/sampling_dist.png?raw=true" height = "75%" width = "75%">

It can be useful to think of standard error as a representation of the error between the mean of a sample and the mean of a population given a sample size of n. As sample size grows, our sampling error decreases, and the standard error decreases. This means the standard deviation of the sampling distribution decreases, and the distribution becomes thinner. More area (probability) is concentrated around the mean.

***The Sampling Distribution is important because statistical tests are practically always dealing with means of samples, not individual data points***

### Combining Random Variables
Given distribution A and B, what is the probability of the difference between a value from A and a value from B being > 20? To answer a question like this, we create a new normal distribution that represents this difference.

<img src="/images/hypothesis_testing/double_dist.png?raw=true" height = "75%" width = "75%">

To create this distribution of the difference, we need a mean and standard deviation. The mean of the difference is simply the difference of the sample means. The variance of the difference is the of the sum their variances. Recall, the variance is the standard deviation squared. So to arrive at the standard deviation of the difference we square the sum of the variances.
Distribution of Differences with mean = 10, and standard deviation ≈ 7.1To solve our original problem, we construct our distribution of differences (above in yellow), determine how many standard deviations the value in question is from our mean, and relate that value to a probability. A value of 20 is about 1.41 standard deviations from the mean, and the associated probability from a z-table is 92%. Subtracting that value from 1 leaves us with 8%. So there is an 8% chance of drawing a single value from A and B and their difference being greater than 20.
That example tests for single data points. What if we wanted to test sample means? We use the Sampling Distribution.
Given distribution A and B, find the probability of the difference between a sample mean from A and a sample mean from B being > 20, with sample sizes of 30. The idea is similar, but moving from single points to sample means, we use the Sampling Distribution. Instead of using the values for mean and standard deviation from A and B, we are using the values of the sampling distributions for A and B. This means we will be using standard error.
The standard deviation of this new distribution will be the square root of the summed variances from the sampling distribution. In the case of using the Sampling Distribution, this is equivalent to the square root of the summed squared standard errors from the original distributions.
Distribution of Differences (for sample means) with mean = 10, standard deviation ≈ 1.3.As expected, this distribution is much more narrow (note the change in scale on the x-axis). A value of 20 is > 7 standard deviations from the mean! That value is so extreme it's not even listed on a z-table, so it is incredibly improbable.
Note how a difference of 20 is far less probable as a sample mean than a single value.

### The T-Distribution
We need a tool to further represent the uncertainty that comes along with small sample sizes. Enter the T-Distribution. It's general shape is similar to the normal distribution, however it migrates some of the area normally found near the mean out to the tails. This makes extreme values more probable. The T-Distribution is defined by its degrees of freedom, which is equal to the sample size - 1. As seen in the chart below, there is a different T-Distribution for every sample size. The smaller the sample size, the "fatter" the tails.
The T-Distribution may look a little different, but its standard deviations are still tied to probabilities. To associate a value with a probability, we just need to know the value in standard deviations and look up this value in a t-table instead of a z-table. That is the only change! We use the t-distribution when we have small sample size or don't know the population standard deviation.

### Bernoulli Trials and the Binomial Distribution
Plotting the distribution of a continuous variable is intuitive, but how do we create distributions for random variables with a binary outcome? For example, if a user did or did not churn. These are called Bernoulli trials. The Binomial distribution is a discrete probability distribution that represents the outcome of repeated independent Bernoulli trials. Think of testing if each individual user churns as a single trial. The distribution is similar to the normal in that it is defined by two parameters: the mean and the standard deviation.
Binomial Distribution with p = 0.20 and n = 1000The distribution above represents the outcome of repeating a trial with a 20% success rate 1000 times. While the calculation of the mean and standard deviation are different, the way we use the distribution is very similar. To see how probable a value is, we find how many standard deviations it is from the mean. When working with samples, we use the sampling distribution. And when looking at the difference between two binomial distributions, we subtract means and add variances. The methods are the same, we just plug different values for mean and standard deviation into them.

### The A/B Test
The A/B test is a hypothesis test, so we frame our problem in terms of a null and alternative hypothesis. All of the concepts applied in an A/B test have been covered, and now we will see how they fit together in this framework.
Our problem at Netflix was to determine if the new page has meaningful impact on behavior. Let's take a look at user conversion rate from a free trial. We have a control cohort which is shown the old page and a test cohort which is shown the new page. Framing our problem, we say we are looking to see if there is a meaningful difference between the conversion rates of the two groups. More specifically, lets test to see if the test group had a higher conversion rate than the control.
- Null hypothesis: Difference = 0
- Alternative Hypothesis: Difference ≠ 0
Note: we are going to perform a test on the difference of two sample means. So we will use the distribution of differences, and because we are working with sample means we will be using sampling distributions. We run the experiment, and get the following results:
It appears the test group had a higher conversion rate by 1.5%. Let's plot those two Binomial distributions:
They do appear to be distinct, however that is not enough evidence for us to say the difference has statistical significance.
For our test, we will construct distributions for our null and alternative hypothesis. For the mean values, the null will be set to zero, and the alternatives will be equal to the difference we saw in the test (1.5%). The standard deviations for both will be equal to the square root of summed variances of the sampling distributions.
The Null and Alternative HypothesisTo arrive at our associated probability, we find out how far our sample means are apart in units of standard deviation for the null hypothesis.
This leaves us with a t-value of 2.13, and the associated probability in a t-table returns ~0.98. That means the probability of drawing a value greater than or equal to 0.015 from the above distribution is ~2%.
This test probability associated with the distance from our mean in standard deviations is called a p-value. It represents the probability of returning this particular value if it came from the null distribution. Said another way, it's the probability we say a value this extreme by change, and both samples did come from the same population.
So, there is a ~2% chance that our samples come from the same population. Can we say that their difference is statistically significant? Before starting a test, it is common practice to set a confidence level we would like to achieve with our test. It is common for this value to be 95%. This is to say if we repeated this test many times, 95% of the time we would conclude the correct result. Confidence levels are tied to a parameter alpha, which is equal to 1 - the confidence level, so normally 5%.
For our test to obtain statistical significance, our p-value must be less than alpha. Which in our case it is, our p-value was 0.02, which is lower than the default alpha of 0.05. So we can conclude with > 95% confidence that these two groups are significantly different!

### Wrap Up:
There are some other important topics to cover on statistical testing such as power, error types, and test design, but hopefully some of the more technical aspects have been made more clear in this article. The central idea behind these types of tests is to determine which distribution to construct, find out how far some test value is from the mean in units of standard deviation, then associate that distance with a probability!
