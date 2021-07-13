# Understanding the impact of customer churn

**Description:** In this post I want to share some of the takeaways I've found to be interesting concerning customer churn and the impact it has on a business. . 

### What is churn?
Customer churn is when a service provider loses a customer. For example, let's say Netflix had 1 million customers at the beginning of the month, and of that 1 million 70,000 cancelled thier subscription and are no longer customers. There 70,000 customer are said to have "churned", and the churn rate for this particular month would be 7%. 70,000 customers *feels* like a very large number, however that is a very common churn rate to see in any subscriptoin business!

There are a few things I would like to cover in this post that I've found impactful:
- How churn relates to customer tenure (how long the average customer remains a customer)
- How churn relates to LTV
- Churn and LTV : CAC
- How churn relates to growth

### Churn and Tenure:
Tenure is a commonly used term to describe the amount of time the average customer remains a subscriber. Fotunately this is a very easy metric to salculate, and is purely a function of the average churn rate.

- Tenure = 1 / churn

<img src="/images/blog_post_3/churn_vs_tenure.PNG?raw=true" width="70%" height="70%">

This is very interesting, as you can see customer tenure varies to a large degree on the left-side of the "elbow" from rate of 1-5%, but far less so for rates 5%+. Marginal decrements to churn rate have a much larger impact the smaller they already are, however it becomes much more difficult to decrease churn as well. It might not be obvious at this point why tenure is so important, so the next relationship we will look at is the Life Time Value (LTV) of a customer vs. churn.

### LTV vs. Churn:
The life time value of a user is the total amount of revenue collected from users on average, less the average cost incurred the users. In this example we assume the following values:
- ARPU: Average Revenue Per User (monthly) = $50
- COGS: Average Cost Incurred by User (monthly)  = $40
- GP: Gross Profit (monthly) = $10
-**LTV = GP * Tenure**

If $10 is the gross profit monthly from each user, and by reducing churn we increase tenure, the LTV of the average customer is raised.

<img src="/images/blog_post_3/ltv_vs_churn.PNG?raw=true" width="70%" height="70%">

The chart appear nearly identical from the once above. Both represent a **inverse/power law** relationship, which is interesting. Once again most of the incremental change due to churn is found at the small values of churn rate. Moving from 8% churn to 7% churn raises LTV from $125 to $142, which is an increase of ~ 14%. However moving from 6% churn to 5% churn raises LTV from $166 to $200, an increase of ~20%. That is a relatively large delta, considering neither of those intervals fall within the "elbow" of the plot where the deltas are the most extreme. 

### LTV : CAC vs Churn:
An important metric for any subscription business is LTV:CAC, which is the ratio of the life time value of the customer to the total cost of acquiring a customer. As you might have guessed, this relationship is a function of churn and therefore is goverened by the power law. This is a signal of customer profitability as well of overall efficiency, most healthy growing companies have a value around 3. This means they are spending ~ 33% of thier customers life time value to acquire said customer.

<img src="/images/blog_post_3/ltv_cac_churn.PNG?raw=true" width="70%" height="70%">

### Churn and Growth: 
In the plot below the growth of a user base through time is shown for different churn rates. The churn rates range from 1% to 14% and the additional assumptions are seen below:
- User base at time 0 = 10,000 users
- Monthly growth = 700 users/month

<img src="/images/blog_post_3/churn_vs_growth.PNG?raw=true" width="70%" height="70%">


Starting with the most intuitive case, look at the black dotted line which represents 7% churn (or losing 700 users/month). It is flat becuase our growth is a static addition of 700 user per month, balancing out our gains/losses.
- As we move to higher churn rates and down the y-axis of the plot we can see that early time (months 0-10) we see large drops in user count from our baseline case, however the relationship *apears* asymptotic and "dies out" around 5000 users. In fact, we know that the point at which the relationship will stablize and flaten is when our addition of 700 users/month comprises the exact percentage of our total user base as our churn rate. So at 15% churn our asymptotic value would be 4,667. So, we would see some decline from 5000, albeit slow




