---
layout: post
title:  "Displaying Percentages in Cognos Embedded Dashboards"
date:   2020-06-01 20:30:09
categories: feature
tags: 
comments: true
image:
---
It's not as simple as you would think.

This article assumes that you have a basic working knowledge of Cognos Embedded Dashboards. If you are not familiar or would like to learn more, check out [the documentation](https://cloud.ibm.com/docs/cognos-dashboard-embedded?topic=cognos-dashboard-embedded-overview_of_cde).

It turns out there is not a **simple way** to display a percentage in Cognos Embedded Dashboards, depending on the type of data you have. Here are the directions for taking a string from your .csv file and displaying it as a percentage on your dashboard.

---

### Step 1

[![](/assets/article_images/2020-06-01-displaying-percents-in-cognos-dashboards/cognos-percent-01.png)](/assets/article_images/2020-06-01-displaying-percents-in-cognos-dashboards/cognos-percent-01.png)
  
Click and drag the field you want to display from the left to the dashboard and change the display type to Summary. Click the ellipses beside `coverage` in the display and click `Calculation.`

---

### Step 2

[![](/assets/article_images/2020-06-01-displaying-percents-in-cognos-dashboards/cognos-percent-02.png)](/assets/article_images/2020-06-01-displaying-percents-in-cognos-dashboards/cognos-percent-02.png)

The calculation editor will open. In that window, paste the following:

```java
_round ( cast(City_of_Markham_KeyMetrics_csv.coverage, float), 5)
```

Replace `City_of_Markham_KeyMetrics_csv.coverage` with your file name and field name in the format [file-name].[field-name]. Then click `OK`
This will convert the data from a string to a floating-point number rounded to 5 decimal places. When you create a calculation on a certain field, it will actually create a new data field from the calculation you created. For instance, consider if I have a data field named total that equals 34, then I apply a calculation on it... something like `total / 2`. This new data field will be either given an automatic name or I can name it in the calculation editor field. In this case, let's say I am naming this new field total2. Once I save the calculation, the new field, total2, will be saved in the left data pane for me to drag and drop onto the dashboard as total2.

---

### Step 3

[![](/assets/article_images/2020-06-01-displaying-percents-in-cognos-dashboards/cognos-percent-03.png)](/assets/article_images/2020-06-01-displaying-percents-in-cognos-dashboards/cognos-percent-03.png)

From here, click the ellipses beside `coverage` in the widget and click `Remove`. Then click the newly-created `coverage %` field on the left side in the data pane and drag to where you just deleted the coverage field from the widget (next to the #).

Note: Your new field may be named something other than `coverage %`.

---

### Step 4

[![](/assets/article_images/2020-06-01-displaying-percents-in-cognos-dashboards/cognos-percent-04.png)](/assets/article_images/2020-06-01-displaying-percents-in-cognos-dashboards/cognos-percent-04.png)

Now we want to format our decimal as an actual percentage. Click the ellipses then click `Calculation`.

---

### Step 5

[![](/assets/article_images/2020-06-01-displaying-percents-in-cognos-dashboards/cognos-percent-05.png)](/assets/article_images/2020-06-01-displaying-percents-in-cognos-dashboards/cognos-percent-05.png)

Click the `%` in the middle dropdown in the second row. Then in the far-right field type `100`. Click `OK`. This will convert your number to a percentage. You can give this field a custom name if you want by changing the `Column name` text. By default, my field will be named `coverage % % 100`.

---

### Step 6

[![](/assets/article_images/2020-06-01-displaying-percents-in-cognos-dashboards/cognos-percent-06.png)](/assets/article_images/2020-06-01-displaying-percents-in-cognos-dashboards/cognos-percent-06.png)

This will create yet another new field in the left pane (mine is named `coverage % % 100`). Remove the `coverage %` field from the graphic by clicking the ellipses and clicking `Remove.` Then drag the newly-created `coverage % % 100` field (or whatever you named it) to the graphic beside the `#` symbol.

That's it! Keep in mind that when making calculations on data fields in Cognos Embedded Dashboards the software will create a new field. So you end up creating a new field each step of the way.

---

I hope this was helpful, and please feel free to [reach out](http://themorgan.io/contact) if you need any help!
