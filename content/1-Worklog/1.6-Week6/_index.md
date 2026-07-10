---
title: "Worklog Week 6"
date: 2026-06-01
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Week 6 Objectives:

- Learn the overview of cost management on AWS using AWS Budgets.
- Understand the role of AWS Budgets in monitoring, controlling, and alerting AWS service costs.
- Practice creating a Budget by using an available template.
- Create a Cost Budget to monitor monthly AWS spending.
- Create a Usage Budget to monitor AWS resource usage.
- Learn about Reservation Budget for monitoring Reserved Instances.
- Learn about Savings Plans Budget for monitoring Savings Plans.
- Configure alert thresholds and email notifications.
- Check Budget history, Budget health, and alert status.
- Clean up the created Budgets after completing the lab.

---

### Tasks to be Completed This Week:

| Day | Task | Start Date | Completion Date | Reference |
| --- | --- | --- | --- | --- |
| Monday | - Read the overview of the Cost Management with AWS Budgets workshop <br> - Learn the role of AWS Budgets in AWS cost management <br> - Access AWS Billing and Cost Management <br> - Get familiar with the Budgets interface in AWS Console | 01/06/2026 | 01/06/2026 | https://000007.awsstudygroup.com/1-create-budget/ |
| Tuesday | - Create a Budget using an available template <br> - Select the Monthly cost budget template <br> - Enter the Budget name, monthly budget amount, and alert threshold <br> - Check the created Budget and view Budget history | 02/06/2026 | 02/06/2026 | https://000007.awsstudygroup.com/1-create-budget/ |
| Wednesday | - Create a Cost Budget using Customize mode <br> - Select Cost budget as the budget type <br> - Configure Period, Budget effective dates, Budgeting method, and Budgeted amount <br> - Configure Budget scope for All AWS services and Unblended costs | 03/06/2026 | 03/06/2026 | https://000007.awsstudygroup.com/2-cost-budgets/ |
| Thursday | - Configure alert thresholds for the Cost Budget <br> - Add an email address to receive budget alerts <br> - Review the Cost Budget configuration <br> - Create the Budget and confirm its active status | 04/06/2026 | 04/06/2026 | https://000007.awsstudygroup.com/2-cost-budgets/ |
| Friday | - Create a Usage Budget to monitor resource usage <br> - Select Usage budget in Budget types <br> - Configure Usage type groups, such as EC2: ELB - Running Hours <br> - Set usage limits and alert thresholds | 05/06/2026 | 05/06/2026 | https://000007.awsstudygroup.com/3-usage-budget/ |
| Saturday | - Learn about Reservation Budget for Reserved Instances <br> - Learn about Savings Plans Budget for Savings Plans <br> - Practice or review the steps to create RI Budget and Savings Plans Budget <br> - Configure coverage threshold, utilization threshold, and email alerts | 06/06/2026 | 06/06/2026 | https://000007.awsstudygroup.com/4-reservation-budget/ <br> https://000007.awsstudygroup.com/5-saving-plans-budget/ |
| Sunday | - Check the list of created Budgets <br> - View Budget health and Budget history <br> - Delete the Budgets created during the lab <br> - Record results, difficulties, solutions, and lessons learned | 07/06/2026 | 07/06/2026 | https://000007.awsstudygroup.com/6-clean-up/ |

---

### Week 6 Results:

#### Knowledge

**What is AWS Budgets?**

- AWS Budgets is a service used to monitor AWS costs, resource usage, and cost-saving commitments.
- AWS Budgets allows users to set budgets by day, month, quarter, or year.
- When costs or usage exceed the configured threshold, AWS Budgets can send alert notifications by email.
- AWS Budgets is useful for beginners because it helps control costs while practicing AWS labs.
- AWS Budgets only monitors and sends alerts. It does not automatically stop AWS resources when the budget is exceeded.

**AWS Billing and Cost Management**

- Learned how to access AWS Billing and Cost Management from the AWS Management Console.
- Understood that this service is used to manage billing information, budgets, cost alerts, and cost reports.
- Learned how to use the Budgets menu to create, view, edit, and delete Budgets.
- Learned how to check the status of a Budget after creating it.

**Create Budget Using Template**

- Understood that templates help create Budgets quickly with predefined configurations.
- Learned how to select Use a template.
- Learned how to choose the Monthly cost budget template.
- Learned how to enter the Budget name, monthly budget amount, and alert threshold.
- Learned how to check Budget history after the Budget is created.
- Understood that templates are suitable for quickly setting up basic cost alerts.

**Cost Budget**

- Understood that Cost Budget is used to monitor AWS spending.
- Learned how to create a Cost Budget using Customize mode.
- Learned how to select Cost budget as the Budget type.
- Learned how to configure the Budget period, such as Daily, Monthly, Quarterly, or Annually.
- Learned the difference between Recurring Budget and Expiring Budget:
  - Recurring Budget repeats based on the selected period.
  - Expiring Budget is applied only within a specific time range.
- Learned how to choose the Budgeting method:
  - Fixed: the same budget amount for each period.
  - Planned: different budget amounts for different months.
- Learned how to set Budget scope to All AWS services.
- Learned how to select Unblended costs.
- Learned how to add alert thresholds to receive notifications when costs reach the configured limit.

**Usage Budget**

- Understood that Usage Budget is used to monitor resource usage instead of cost.
- Learned the difference between Cost Budget and Usage Budget:
  - Cost Budget monitors spending.
  - Usage Budget monitors resource usage.
- Learned how to select Usage budget as the Budget type.
- Learned how to choose Usage type groups to define the resource type that needs to be monitored.
- In this lab, Usage Budget can be used to monitor EC2: ELB - Running Hours.
- Learned how to set usage limits.
- Learned how to configure email alerts when usage exceeds the threshold.
- Understood that Usage Budget is useful for services that are charged by usage hours, such as EC2, Load Balancer, or other continuously running resources.

**Reservation Budget**

- Understood that Reservation Budget is used to monitor Reserved Instances.
- Learned that Reserved Instances usually require commitment or upfront payment, so they should only be reviewed or practiced carefully in a lab environment.
- Learned that Reservation Budget helps monitor Reserved Instance coverage or utilization.
- Learned how to configure the coverage threshold.
- Understood that Reservation Budget is suitable for business environments that use Reserved Instances to optimize long-term costs.

**Savings Plans Budget**

- Understood that Savings Plans Budget is used to monitor Savings Plans.
- Learned that Savings Plans allow users to commit to a certain level of compute usage over a period of time in exchange for lower prices compared to On-Demand pricing.
- Learned that Savings Plans Budget helps monitor utilization thresholds.
- Learned how to configure email alerts when Savings Plans usage does not meet expectations.
- Understood that Savings Plans Budget is useful when the system has stable compute usage over a long period.

**Alert Threshold and Email Notification**

- Understood that alert threshold is the limit used to trigger a budget warning.
- Learned how to configure multiple alert levels, such as 50%, 80%, and 100%.
- Learned how to add an email address to receive budget alerts.
- Understood that the email recipient should be the person responsible for monitoring AWS costs.
- Learned that AWS Budgets can send notifications when the cost or usage reaches the configured threshold.

**Budget Health and Budget History**

- Understood that Budget health shows the current status of the budget compared to the configured limit.
- Learned how to use Budget history to view cost or usage trends over time.
- Learned how to evaluate whether the AWS account is using more resources than expected.
- Understood that AWS cost and usage data may have some delay before being fully updated.

**Clean Up AWS Budgets**

- Understood that cleanup is an important step after completing the lab.
- Learned how to delete the Budgets created for practice.
- Understood that deleting AWS Budgets does not delete running AWS resources.
- Learned that AWS Budgets is only a monitoring and alerting tool.
- Learned how to check the Budget list after deletion to make sure unnecessary alerts are removed.

---

#### Practice

**Module 1 — Create Budget**

- Accessed the AWS Management Console.
- Opened AWS Billing and Cost Management.
- Selected Budgets from the left menu.
- Clicked Create a budget.
- Selected Use a template.
- Chose the Monthly cost budget template.
- Entered the Budget name.
- Entered the monthly budget amount.
- Configured the alert threshold.
- Clicked Create budget.
- Checked that the Budget was created successfully.
- Opened Budget history to view the budget status.
- Reviewed the available alert types in the template.
-  _Evidence: AWS Billing and Cost Management interface._
-  _Evidence: Budgets page and Create a budget button._
-  _Evidence: Monthly cost budget template selected._
-  _Evidence: Budget created successfully._

**Module 2 — Create Cost Budget**

- Accessed AWS Billing and Cost Management.
- Selected Budgets from the left menu.
- Clicked Create budget.
- In Budget setup, selected Customize.
- In Budget types, selected Cost budget.
- Clicked Next.
- In Set your budget, entered the Budget name `Monthly`.
- Selected the Period, such as Monthly.
- Configured Budget effective dates:
  - Recurring Budget for repeated budgets.
  - Expiring Budget for one-time budgets.
- Configured the Budgeting method:
  - Fixed for the same budget amount each period.
  - Planned for different budget amounts by month.
- Entered the Budgeted amount.
- In Budget scope, selected All AWS services.
- In Aggregate costs by, selected Unblended costs.
- Clicked Next to configure alerts.
- Selected Add an alert threshold.
- Entered the alert percentage, such as 80% or 100%.
- Entered the email address for notifications.
- Reviewed all configurations.
- Clicked Create budget.
- Confirmed that the Budget appeared in the Budgets list.
-  _Evidence: Customize and Cost budget selected._
-  _Evidence: Period, Budget amount, and Budget scope configured._
-  _Evidence: Alert threshold configured._
-  _Evidence: Cost Budget created successfully._

**Module 3 — Create Usage Budget**

- Opened Budgets in AWS Billing and Cost Management.
- Clicked Create budget.
- Selected Customize.
- In Budget types, selected Usage budget.
- Clicked Next.
- Entered the Usage Budget name.
- In Budget against, selected Usage type groups.
- Chose the usage type to monitor, such as EC2: ELB - Running Hours.
- Configured Set budget amount:
  - Selected Period as Daily, Monthly, Quarterly, or Annually.
  - Selected Budget renewal type as Recurring or Expiring.
  - Selected Budgeting method as Fixed or Planned.
  - Entered the maximum number of usage hours to monitor.
- Kept the default Budget scope if suitable for the lab.
- Clicked Next to configure alerts.
- Selected Add an alert threshold.
- Entered the alert percentage.
- Entered the notification email address.
- Reviewed the configuration.
- Clicked Create budget.
- Checked Budget health to view current usage compared with the limit.
- Opened Budget history to track usage trends.
-  _Evidence: Usage budget selected._
-  _Evidence: Usage type groups selected._
-  _Evidence: Usage hours and alert configured._
-  _Evidence: Usage Budget displayed in the Budgets list._

**Module 4 — Create RI Budget**

- Accessed AWS Billing and Cost Management.
- Selected Budgets.
- Clicked Create budget.
- In Budget setup, selected Customize.
- Selected Reservation budget.
- Clicked Next.
- Entered the Reservation Budget name.
- Configured the Coverage threshold to monitor Reserved Instance coverage.
- Configured Budget scope according to the lab instructions.
- In Alert setting, entered the email address for notifications.
- Reviewed the information.
- Clicked Create budget.
- Checked that the Reservation Budget was created.
- Noted that in the lab environment, purchasing Reserved Instances is not required because it may generate costs or long-term commitments.
-  _Evidence: Reservation budget selected._
-  _Evidence: Coverage threshold configured._
-  _Evidence: Reservation Budget created or reviewed in demo mode._

**Module 5 — Create Savings Plans Budget**

- Accessed AWS Billing and Cost Management.
- Selected Budgets.
- Clicked Create budget.
- Selected Customize.
- In Budget types, selected Savings Plans budget.
- Clicked Next.
- Entered the Savings Plans Budget name.
- Configured the Utilization threshold to monitor Savings Plans usage.
- Kept the Budget scope as default if suitable for the lab.
- In Alert setting, entered the email address for notifications.
- Reviewed the full configuration.
- Clicked Create budget.
- Checked the created Budget in the Budgets list.
- Noted that Savings Plans usually involve long-term usage commitments, so they should only be reviewed or practiced carefully in a learning account.
-  _Evidence: Savings Plans budget selected._
-  _Evidence: Utilization threshold configured._
-  _Evidence: Savings Plans Budget created or reviewed in demo mode._

**Module 6 — Clean Up Resources**

- Accessed AWS Billing and Cost Management.
- Selected Budgets from the left menu.
- Checked the list of Budgets created during the lab.
- Selected the Budget to delete.
- Chose Action.
- Clicked Delete.
- Confirmed the deletion in the confirmation dialog.
- Repeated the same steps for the remaining Budgets created during the lab.
- Checked the Budgets list again after cleanup.
- Noted that deleting a Budget only removes the monitoring and alert configuration. It does not affect any running AWS resources.
-  _Evidence: Budget list before deletion._
-  _Evidence: Delete Budget action._
-  _Evidence: Budget list after cleanup._

---

### Week 6 Evaluation:

- Completed the study of AWS Budgets for AWS cost management.
- Learned how to create a Budget using an available template.
- Created a Cost Budget to monitor monthly AWS spending.
- Created a Usage Budget to monitor AWS resource usage.
- Understood the difference between Cost Budget and Usage Budget.
- Understood the purpose of Reservation Budget for Reserved Instances.
- Understood the purpose of Savings Plans Budget for Savings Plans.
- Learned how to configure alert thresholds and email notifications.
- Learned how to check Budget health and Budget history.
- Learned how to clean up Budgets created during the lab.
- Became more aware of the importance of cost control when practicing AWS labs.

---

### Difficulties Encountered:

- The Billing and Cost Management interface contains many sections, so it was easy to confuse Bills, Cost Explorer, and Budgets at first.
- It was necessary to clearly distinguish Cost Budget from Usage Budget because they monitor different types of information.
- When configuring Usage Budget, it was important to choose the correct Usage type group.
- Some new AWS accounts may not show all Budget types immediately.
- AWS cost and usage data may be delayed, so the Budget may not show full data immediately after creation.
- RI Budget and Savings Plans Budget were more difficult to understand because they are related to long-term cost commitments.
- If the alert email is entered incorrectly, the user may not receive budget notifications.
- AWS Budgets only sends alerts. It does not automatically stop AWS services when the budget is exceeded.

---

### Solutions:

- Read each step in the workshop carefully before performing actions in the AWS Console.
- Take notes on the purpose of each Budget type:
  - Cost Budget monitors spending.
  - Usage Budget monitors resource usage.
  - Reservation Budget monitors Reserved Instances.
  - Savings Plans Budget monitors Savings Plans.
- When configuring alerts, enter the correct email address and check the mailbox for AWS notifications.
- Set multiple alert levels, such as 50%, 80%, and 100%, to control costs more effectively.
- Do not purchase Reserved Instances or Savings Plans in a learning account unless required.
- Delete practice Budgets after completing the lab to avoid unnecessary notifications.
- Check the Billing Dashboard after each lab session to detect unexpected costs.
- Combine AWS Budgets with regular resource cleanup habits to reduce the risk of unexpected AWS charges.

---

### Plan for Next Week:

- Continue learning about AWS Billing and Cost Management.
- Study AWS Cost Explorer to analyze costs by service, time, and account.
- Learn how to use the AWS Free Tier Dashboard to monitor free tier limits.
- Learn about AWS Cost Anomaly Detection to identify unusual spending.
- Study AWS Organizations and cost management for multiple AWS accounts.
- Practice checking the Billing Dashboard after each AWS lab.
- Prepare evidence screenshots, notes, and comments to complete the next weekly report.
