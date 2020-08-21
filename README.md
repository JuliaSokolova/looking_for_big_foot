# Looking for Big Foot with NLP Unsupervised Learning

A data set of bigfoot sighting reports was collected from the <a href='http://www.bfro.net/gdb/'>Geographic Database of Bigfoot / Sasquatch Sightings & Reports</a>.
We explored the dataset using basic data exploratory algorithms, Natural Language Processing and Topic Modeling with NMF libraries.

## Table of Content

- [Objective](#objective)
- [Approach](#approach)
- [Challenges](#challenges)
- [Discoveries](#Discoveries)
- [Conclusion](#conclusion)


<br>

## Objective 

Find something interesting in the data! For example:
- What state has the most UFO/bigfoot sightings? Can you visualize this/compare them?
- What kind of language is used to describe UFO vs. bigfoot sightings. Does it vary by region or time?


<br>

## Approach

- Use data gathered from Geographic Database of Bigfoot / Sasquatch Sightings & Reports (json file)
- Process data with BeautifulSoup
- Explore insights with Python scikit-learn library

<br>

## Challenges

- Extracting data from json file
- Cleaning and preprocessing the data 

Here are some code examples from our data cleaning pipline:

```
def convert_report(report:dict) -> BeautifulSoup:
    return BeautifulSoup(report['html'], 'html.parser')
def parse_report(cvt_report:BeautifulSoup) -> dict:
    feat_val = build_details([x.get_text() for x in cvt_report.find_all('p')])
    #feat_val_dict = {x[0].lower().strip():x[1] for x in feat_val}
    return feat_val
```


```
def build_header(str_lst):
    """
    Parameters
    ----------
    str_list: list(str)
        A list of strings
    Returns
    -------
    result : dict
        A dictionary with key, value pairs from processing the header
    """
    result = {}
    # array for geographical information 
    # Example : (Geographical Index > United States > Alaska > Anchorage County > Report # 13038)
    # geographic = [Geographical Index, United States, Alaska, Anchorage County, Report # 13038]
    geographic = [x.strip() for x in str_lst[0].split('>')]
    geo_dict = {}
    geo_dict['region'] = geographic[1]
    geo_dict['state'] = geographic[2]
    geo_dict['detail'] = geographic[3]
    report = geographic[4]
    geo_dict['report'] = report.split(' ')[-1]
    geo_dict['report_class'] = str_lst[2]
    return geo_dict
def build_details(str_lst):
    """
    Parameters
    ----------
    str_list: list(str)
        A list of strings
    Returns
    -------
    result : dict
        A dictionary with key, value pairs from processing the details section
    """
    # empty list to store return key value pairs
    result = {}
    # flag to alter the way we build keys
    process_diff = False
    # empty array to store extra information if process_diff flag is True
    extra = []
    for i, x in enumerate(str_lst):
        # splits each line into a key, value string pair
        first, *second = x.split(':')
        if not first.isupper():    
            # checks if key isn't CAPS, if not we process the rest of the html differently
            process_diff = True
        if not process_diff:
            # we join the second part in case there exists a colon ":" inside the raw text
            result[first.lower().strip()] = (' '.join(second)).strip()
        else:
            # append each extra line to a general array
            extra.append(x)
    # combine all extra values into a single long string
    result['extra'] = ' '.join(extra)
    return result
```

```
ata = pd.DataFrame(columns=['year', 'season', 'month', 'state', 'county', 'location details', 'nearest town', 'nearest road', 'observed', 'also noticed', 'other witnesses', 'other stories', 'time and conditions', 'environment', 'extra'])
for report in reports:
    cvt_report = convert_report(report)
    parsed = parse_report(cvt_report)
    to_append = pd.Series(parsed)
    data = data.append(to_append, ignore_index=True)
```
<br>

## Discoveries

Big Foot poplation was most noticeble between 1998 and 2014. In the past few years, people see less and less Big Foor, which can be explained by climat change and human overpopulation. 
<p align="center"><img width=100% src=https://github.com/JuliaSokolova/looking_for_big_foot/blob/master/img/yearly_bigfoot_coutns.png> 
  
  
Most often, people see Big Foot in Washington and California state. We believe it can be correlated with legalization of weed in those states.
<p align="center"><img width=100% src=https://github.com/JuliaSokolova/looking_for_big_foot/blob/nicole/images/top_10_states.png> 
  
In case you want to go look for a Big Foot yourself, here is the most popular counties:

<p align="center"><img width=100% src=https://github.com/JuliaSokolova/looking_for_big_foot/blob/master/img/bigfoot_sightings_by_county.png> 


Big Foot get more active during July - December. In January - June they get more introverted. 
The graph below shows how often people see Big Foots by months.

<p align="center"><img width=100% src=https://github.com/JuliaSokolova/looking_for_big_foot/blob/nicole/images/month_frequency.png> 


Finally, we have found how people usually describe their experience when meeting a Big Foot:


<p align="center"><img width=100% src=https://github.com/JuliaSokolova/looking_for_big_foot/blob/nicole/images/The_top_10_words_in_observed_topics.png> 
  
  
  

