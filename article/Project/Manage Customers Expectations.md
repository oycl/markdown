# The Key to Implementation Projects: Manage Customers Expectations

I am a developer. I like to develop; it's fun and intellectually challenging.

But, as the CEO of Odoo, I also know that, for ERP implementation projects, custom developments should be avoided as much as possible.

It's not as easy as it may seem when customers often think they need custom developments. On the other hand, implementation service companies are happy to charge extra service/development days for these customizations. But I have to warn both sides, custom developments are not good for you!

## Why Minimize Custom Developments?
**For customers**, a custom development creates additional cost and timeline delays to the implementation project, sometimes to the point of putting the project at risk. In addition, custom development leads to a technical debt that you will have to pay for within the coming years in the form of more maintenance and upgrade costs. Such a technical debt costs around 25% of the development cost EVERY YEAR (~17% in maintenance + ~8% in upgrades).

Each development may seem simple and affordable. But the complexity of a project grows with the square of the number of customizations, not linearly. You probably want to solve what you didn’t like in your previous software; but wouldn't it be better to standardize your business processes and implement the project 2x faster and cheaper?

**For partners**, custom developments usually come at a high cost, for a low customer value. How often have you estimated 10 days to develop a feature; the customer thinks it's too much for such a basic feature so you charge only 8 days; but you end up spending 12 days. Oh, and when we discover issues/changes later on because you had to do it in a hurry, the customer won't pay for the extra day as it's your fault. What should have been a 35% margin service is now a 6% loss on service!

To grow, it's easier to focus on valuable services having better margins, and decreasing the risk of non-billable hours. Such services include: project management, business analysis, customizations without developments, change management, and training.

If you don't build a mindset of reducing custom developments, sooner or later, you will become uncompetitive. Competitors who better manage customers expectations will have lower project implementation costs. Have you ever lost a project because the customer said "you are too expensive", only to discover that the customer purchased much less than what you would have delivered?

Of course, custom developments are sometimes necessary to run the business. But, from my experience, the majority of customer requests are either not worth the cost for them, not relevant once they start to use Odoo, or they can get by without it as it is not part of their core usage. Will you accept these requests or not? It all depends on your implementation methodology and your company mindset.

The customer is likely not an expert in the product yet, and probably not in implementation projects either. Thus, they cannot easily balance the cost of a specific feature against the revenue they will gain from it. Customer requests are based on the challenges they had with their old software or current way of working. Most of these issues are alleviated or no longer applicable once they transition to using Odoo.

Defining the right balance between standard and custom developments is not easy. What might not be worth it for one customer can be very valuable for another.

If you ask services companies, they will all tell you that they develop only what's needed by the customer (and they really think they do). In reality, it's very hard to assess yourself and know if you are good at evaluating return on investments, and solving problems with good business advice instead of developments.

To understand the different mindsets, let's look at the implementation structure of two service companies.

**CASE 1**: If a company has 8 developers and 2 project managers, their focus is to do custom developments. Their methodology is probably Scrum (or another agile one), and their project manager will spend most of their time writing specifications and testing developments. They satisfy customers by developing, but this approach inevitably leads to an increase in the project’s overall costs (not on purpose, of course). Customers are satisfied in the short term (as they get everything they request), but might be dissatisfied as the costs and delays increase.

**CASE 2**: A company having 2 developers and 8 business people (analysts or project managers) focuses on business services: change management, business process engineering, finding standard solutions to business problems, trainings. They will have accountants, inventory managers, or others experts in their team who can challenge customer requests. They satisfy customers because they deliver a very high value at a very good price. Most customers will like the approach, but some might be frustrated in the short-term as you challenge what they request. In contrast, these customers will be highly satisfied in the mid-term as the project moves forward.

What is the right balance between CASE 1 and CASE 2? Unfortunately, there is no benchmark.

At Odoo, we have a service department with developers, project managers, and business experts. Over the years, we have focused on improving project velocity, rather than trying to sell more services upfront.

Here are our team sizes for our direct implementation projects:

* For small customers (1-50 users), we have 11 developers for 80 project managers & business analysts. Thus, a 12% developer profile.

* For large projects (>500 users), our ratio is closer to 50% developers, 50% non-developers.

Or, you can see it another way; on small to mid-size projects, on average, 12% of our time billed is development, 88% is business services.

**As a customer**, this simple trick of checking team sizes will help you assess your potential partner according to what you expect. As a service company, do the math with your own team sizes. This ratio will tell you if you have room for improvement to increase your margins and project velocity.

If your ratio is 2x higher than this one, it’s worth thinking about your methodologies, business model, and recruitment profile. A good starting point is Odoo’s own [“implementation methodology”](https://odoocdn.com/openerp_portal/static/src/pdf/odoo_implementation.pdf).

## How to Deal with Customers' Specific Demands?
When dealing with customers, remember that there is a difference between stakeholders’ objectives, and key user's needs. Most decision makers will tell you that their priority is time and budget, while key users will mostly think in terms of specific features to develop. As these objectives contradict each other, you’ll have to decide; who do you want to satisfy? 

I think you should always do what you think is good for the project; that means challenging what key users request, to the point of refusing to do it if you think it’s not worthwhile for the project. Trying to satisfy key user's demands by doing everything they ask is a very short-term mindset; it’s better to focus on the on the project's success.

I use the following framework to deal with custom development requests:

1. Is it really necessary?
2. Is it worth supporting the cost (of developing and maintaining it)?
3. Is the gain significant enough?
4. Can we serve the same objective, with a different approach?

If you came to the conclusion that developing a specific feature is not worth it, you should try hard to convince the customer. There is different tactics for this: explain the “Why”, phase it in after the "go live" date, escalate to a manager (while not ideal, it’s sometimes necessary).

**Is it really necessary?** 

Let's say you have a request for the following custom development:

The customer has a website developed with Magento Commerce and does not want to change their website as they already invested a lot in it. But they would like Odoo to be completely integrated with their Magento website (including products, coupons, follow-ups on abandoned carts, etc.)

The best way to assess if it’s necessary is to check if the customer already has this feature in their old software. If the customer records all orders manually in their old software, they can do it the same way with Odoo. I would then recommend to go into production without developing an integration with Magento, use Odoo for 3 months, and then review priorities and decide if it’s worth it; at that time, you have a 50% chance that the customer loves Odoo so much that they will go for Odoo eCommerce rather than a connector for Magento. :)

In terms of change management, it’s always better to roll out business process changes progressively, rather than everything all at once. With the modularity of Odoo, it makes sense to deploy in several phases: 1/ With what the customer absolutely needs to operate the business and only after moving to 2/ Other features to improve efficiency.

In reality, the customer’s development priorities will switch when they go into production. Reasons include:

* While using the software, they discover new developments that are more important and re-prioritize. 
* After having spent some money on the implementation, they are happy with the result and prefer to cut spending on unnecessary features. 
* When the customer starts using Odoo in production, their mindset changes as they grow their expertise in the product.

**Is it worth supporting the cost?** 

Secondly, you need to evaluate the benefit; how many man-days per month will the customer save because of this feature. Often, the customer only accounts for how much time they spend on this kind of task currently, and how much they think they will save in the future. In reality, they will still have to record all data necessary for the computation, deal with exceptions manually, etc. (Example: Even if the customer develops a Magento connector, they will still have to solve all conflicts, record price discounts in both software solutions, deal with inventory conflicts as the synchro is not real-time, etc.)

Then, you need to evaluate the cost efficiently. Often, the customer only sees the "one-shot development cost". In reality, you can easily multiply this cost by 2 or 3 as you need to take multiple factors into account: time for testing, bugs and extra delays on the project, adapting documentation as it's not standard, future maintenance and upgrades over the next 5 years.

Note that using a community module allows you to save the time of the initial development, but you'll still have the testing, maintenance, project delays and upgrade to account for in the cost. A community module is also a technical debt too.

**Is the gain significant enough?**

Let's say you have a request for the following custom development:

When we confirm a sale order for a construction project, we want to create a series of tasks based on a set of rules that depends on the products, customer and locations.

When you have a customization request, your first reflex should be to question the volumes: how many construction projects do you win per month? Let's say the customer wins 10 of such projects per month. It probably takes 10 minutes to create and update tasks by duplicating and updating project templates.

Is it worth launching a complex development to save under 2 hours or work per month? Surely not, this feature will cost around 10 days of developments, plus 25% of this every year.

**Can we serve the same objective, with a different approach?**

Let's say you have the following customer request: 

I want to synchronize our Outlook calendar with Odoo CRM. 

Odoo has a connector with Google Calendar in standard, but not with Outlook. But developing and maintaining a connector might cost a lot of money. However, there are plenty of services that synchronize Google Calendar with Outlook (such as IFTTT). So, a solution would be to subscribe and setup such a service for every employee.

It's not perfect as you will have to modify your setup every time you recruit a new employee. But the solution is ready in 2 hours, and it will only take 10 minutes per new employee. It's still much less than a new development, if the company has less than 100 employees.

Note: In reality, Odoo has an Outlook connector in the community apps, so we advise using this one instead. This was just a theoretical example.

## Partners FAQ
### If I reduce custom developments, will I lose significant revenue?
If you have 80% of your team as developers, and 20% as project managers, you might have the feeling that developments are necessary to sustain your revenue. But companies having 20% of their teams as developers have an exact opposite point of view; business services are necessary to sustain revenues, and margins.

In reality, customers need functional services a lot, often more than developments. It’s just that you have to find the right way to sell and service them. Usually, once the customer is live, the number of development requests decrease, while the demand of business services can continue, if your approach is good.

Of course, you cannot switch from one day to another; changing a company mindset and its implementation methodology takes time. But if you can go from 80/20 to 70/30, you will improve your margins. Then go to 60/40 and you will take one more step forward.

My recommendation would be to:

1. Work on your implementation methodology ([start with ours if you don't have one](https://www.odoo.com/openerp_portal/static/src/pdf/odoo_implementation.pdf)).

2. Keep your team, but progressively recruit a few extra business analysts or project managers who do not have developer profile. 

Things to keep in mind:

1. Avoid developers who do project management too. Becoming an expert developer is hard and takes years of practice. Being a great project manager also takes time and experience. If you promote developers into project managers, you will have people average in both roles, not excellent in one; and having average project managers will be detrimental to your implementations. 

2. Avoid using developers in the customer relationship. Developers can do everything; they easily find solutions to technical problems. As a result, it’s easy for them to say “Yes” for a custom development, as they don’t feel the pain of having to manage it. When Odoo only had 10 employees (mostly developers), that was the move that allowed me to grow faster: I started to recruit project managers without development knowledge and better structure everyone’s role.

## If the customer chose Odoo, isn't it because they want all these customizations? 
Odoo is an amazing software. Just by using Odoo standard, the customer’s company will be massively transformed, for the better. Most departments will be more efficient, and employees will have a tool to boost their productivity. This is where the value is; the custom developments will be only 5% to 10% of the features the customer will use from the platform.

You should manage customer expectations, even before making an offer, and the customer will thank you for challenging their requests; this is what they usually expect from great project managers. This will allow you to reduce the initial budget and be more competitive, while limiting costly developments to focus on high margins associated with business services.

Once the customer is in production and happy, they will be much more likely to buy more of your services as your value for the money is great. There are so many apps you can deploy on Odoo that the potential is close to unlimited, you can always enlarge the scope.

Most companies think they are unique, and special, and feel justified in their desire to customize. This is the wrong mindset for a successful project. It’s up to you to steer the project in a value-focused direction that helps the customer, not just fulfilling requests. They will reward you in the long-term for such an approach. Odoo isn't just a platform, it is "best business practices" coded into the software. These are practices distilled from a huge amount of experience during many years working with customer implementations.

## Is it worth developing custom features, if it can be reused on several 'future' customer projects?
In the past, we have tried several times to develop a custom feature for a customer, with the hope to reuse this feature for future customers. It failed in most cases:

* People always have the feeling that a feature is generic enough and a lot of people will want it. In reality, the others customers will want it slightly different and you end up managing different versions of your custom code.

* This argument is often used for the customer to negotiate a way to “share” the cost of a custom feature, or for your internal team to justify an “unbilled” feature. In both cases, it’s not good for the margins of the company.

Developing features to sell to several customers is the business model of a software vendor (like Odoo SA), but it’s a completely different business model than a service company. It’s a model where you need 80% margins on your products to cover the very high R&D costs. It’s a model where >50% of your charges are developers in R&D, not billed to customers.

An efficient service company will target a billing rate around 80%. That’s what you need to sustain healthy growth. You won’t reach that level of billing rate if you have a mixed business model of software development and services. 

## My Apologies
I know such a blog post might not please everyone. My apologies. I certainly don’t want to hurt anyone. I just want to be helpful. And, to be helpful, I have to be direct and transparent.

Please don’t read this blog as the point of view of a “Software Vendor”. These are actually things we have learned over the past year in our service department, focusing on project velocity and competitivity, doing what we think is good for the success of the project, not what key users ask us to do. It turns out this service department is now as disruptive and efficient as our product, to the point that it became a massive competitive advantage.

By sharing what we have learned, I hope it will help some partners to accelerate their growth, just like SAP did a few years ago with ASAP, the Accelerated SAP implementation methodology. ASAP has played a major role in SAP’s evolution and their ability to implement complex corporations, in the early years. A key component of their approach is to stick to the standard, the “best business practices”. If their approach leads to successful implementations with an outdated product like SAP, imagine what we can do with an amazing software like Odoo!

We have a large partner network of very smart people. Our only weakness; we are younger, less mature. If we succeed to grow in maturity, we will disrupt the market, faster than any product has ever done before. We transformed the way 3.7M users work in a few years. But to reach 100M users, a great product is not enough; we have to build the best partner network ever, together.


[back](../)
