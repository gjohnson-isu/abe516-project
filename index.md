---
layout: default
---

Text can be **bold**, _italic_, or ~~strikethrough~~.

[Link to another page](./another-page.html).



# ABE 516 Project
**Gabe Johnson**, PhD Student in Agricultural and Biosystems Engineering and Sustainable Agriculture

This website demonstrates the use of data science concepts learned in ABE 516 applied to my PhD disseration research on saturated buffers. Saturated buffers are an edge-of-field practice for reducing nitrate loss in agricultural subsurface drainage. Current research has shown these practices to be effective at removing nitrate from drainage water, but overall effectiveness is limited by the total amount of flow treated. My larger research project is focused on optimization of these practices for nitrate load reduction, analyzing the effectiveness over a longer period (5+ years), and using empirical data to predict future performance at existing or new sites.

# Research Question
The specific research question for this course project is:

**Can saturated buffer nitrate load reduction be predicted from design parameters?**

# Data Description
Data for this project consists of saturated buffer flow data (flow in, flow treated, flow bypassed), nitrate concentration data from inlet and outlet points (the saturated buffer control structure and various groundwater monitoring wells), and nitrate load data. Nitrate load (mass) is calculated from flow (volume) and concentration data (mass per volume) for annual sampling dates. 

# Data wrangling
## Description
Data wrangling consists of processing flow and nitrate concentration data into clean, quality-controlled files, calculating nitrate load, and summarizing data for individual site-years and across site-years. 

Flow:
* hourly flowrates (in, bypass, treated) calculated from monitored water levels and processed for missing or extreme data
* cumulative flow volumes calculated 

Nitrate Concentration:
* raw concentration data files processed to extract sampling dates and concentration values for sample locations
* creation of clean dataframe or csv file of concentration summarized by sample data and inlet and outlet concentrations

NO3-N Load:
* incremental and cumulative loads (in, diverted, treated) calculated from cumulative flows and clean nitrate concentration data

Site-Year Summary Data:
* Annual summary metrics (total NO3-N load removed, total flow treated, percent NO3-N load reduction, and many others) calculated and organized into one tidy dataframe or csv file

## Implementation
Flow data wrangling can be seen in [this jupyter notebook](./project-wrangling-1.md).

# Data Exploration
insert here

# Models
insert here

# Results
insert here

# Discussion
insert here

_______________________________________________________

# Template Code left temporarily for reference

> This is a blockquote following a header.
>
> When something is important enough, you do it even if the odds are not in your favor.

### Header 3

```js
// Javascript code with syntax highlighting.
var fun = function lang(l) {
  dateformat.i18n = require('./lang/' + l)
  return true;
}
```

```ruby
# Ruby code with syntax highlighting
GitHubPages::Dependencies.gems.each do |gem, version|
  s.add_dependency(gem, "= #{version}")
end
```

#### Header 4

*   This is an unordered list following a header.
*   This is an unordered list following a header.
*   This is an unordered list following a header.

##### Header 5

1.  This is an ordered list following a header.
2.  This is an ordered list following a header.
3.  This is an ordered list following a header.

###### Header 6

| head1        | head two          | three |
|:-------------|:------------------|:------|
| ok           | good swedish fish | nice  |
| out of stock | good and plenty   | nice  |
| ok           | good `oreos`      | hmm   |
| ok           | good `zoute` drop | yumm  |

### There's a horizontal rule below this.

* * *

### Here is an unordered list:

*   Item foo
*   Item bar
*   Item baz
*   Item zip

### And an ordered list:

1.  Item one
1.  Item two
1.  Item three
1.  Item four

### And a nested list:

- level 1 item
  - level 2 item
  - level 2 item
    - level 3 item
    - level 3 item
- level 1 item
  - level 2 item
  - level 2 item
  - level 2 item
- level 1 item
  - level 2 item
  - level 2 item
- level 1 item

### Small image

![Octocat](https://github.githubassets.com/images/icons/emoji/octocat.png)

### Large image

![Branching](https://guides.github.com/activities/hello-world/branching.png)


### Definition lists can be used with HTML syntax.

<dl>
<dt>Name</dt>
<dd>Godzilla</dd>
<dt>Born</dt>
<dd>1952</dd>
<dt>Birthplace</dt>
<dd>Japan</dd>
<dt>Color</dt>
<dd>Green</dd>
</dl>

```
Long, single-line code blocks should not wrap. They should horizontally scroll if they are too long. This line should be long enough to demonstrate this.
```

```
The final element.
```
