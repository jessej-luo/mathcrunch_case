# Yup Case Study

Jesse Luo
This case study was done as part of the application to Yup with the sample data from the tutors exported to tutors.csv and imported into this python3 program. It was done using an iPython Notebook which is inside this repository and can be looked at. It has its own comments, which are also shown here. The work was done in the iPython Notebook and then conclusions were written up here.

## Introduction
The issue at hand is that with the provided rubric, senior tutor scores sometimes differ greatly from the answer key. This makes it difficult to assess the junior tutors as everyone has a significantly different interpretation of the  Given the set of 10 sessions with gradings from 14 tutors on a rubric with 8 criteria, I looked for any possible patterns in the data that would be helpful. I will incorporate my code into my discussion to show how I arrived to these conclusions and how I was able to analyze the data. The preliminary conclusions I came to include a reworking/clarification of certain parts of the criteria, talking to certain tutors to check their understanding of the criteria, and possibly using a different system of evaluating the junior tutors by averaging/dominance of criteria to determine the "average" of all tutors criteria.

## Analysis
When I looked at the criteria initially and tried judging the sessions personally, I found it to be difficult to definitively say whether the tutor accomplished the task at hand or not. The rubric works by either assessing 0 or 1/2, but generally these evaluations require a more subtle scale since sometimes tutors may accomplish it to a certain degree, but not good enough to warrant a full score. Based on this initial impression, I started by looking for possible trends in the use of the criterion.

### Criteria Misinterpretation
I suspected that there would be misunderstandings of the criterion by the tutor, so I examined that first. My end goal was to identify possibly troubling parts of the criteria and understand why. To start, I aggregated the data for each session by all of the tutors and went on to locate the conflict within the decisions on each criteria. I did this with the following: 

```python
# Returns a list with number of yes - number of no for each property
# This basically means that the lower the #, means more conflict 
# between tutors
def variance_finder(session):
    variance = []
    for key, value in session.items():
        num_yes = 0
        num_no = 0
        for decision in value:
            if decision.lower() == "yes":
                num_yes += 1
            else:
                num_no += 1
        variance.append(abs(num_yes - num_no))
    return variance

variance_dict = collections.OrderedDict()

for i in range(0, 10):
    variance_dict["session_" + str(i+1) + "_difference"] = 
    (session_dict_list[i])

# We now have the difference for every single session, and the ones 
# with most conflict will be the ones that have the lowest variance, 
# which means there's a more "split" decision
# which indicates a need for clarification, find a possible general trend

session_var_list = [
        session_1_variance, session_2_variance, 
        session_3_variance, session_4_variance,
        session_5_variance, session_6_variance, 
        session_7_variance, session_8_variance,
        session_9_variance, session_10_variance
    ]

total_variance = []
for i in range(8):
    sum1 = 0
    for item in session_var_list:
        sum1 += item[i]
    total_variance.append(sum1)

# calculating total variance, so basically the total 
# difference of each criteria whether yes or no is 
# dominant here doesn't really matter, it actually is 
# just used to find confusing criteria

total_variance
[104, 112, 120, 90, 98, 100, 50, 88]

# Correlates directly with the first criteria to the last
# and indicates that there are a couple controversial criteria:
#Instruction [Deliver an appropriate explanation that 
#   follows the given instructions?]
#Instruction [Consistently check in with the student to 
#    ensure that they are following along?]
#Overall [Communicate clearly throughout the session?]
```
I first made a function that finds "variance" which in this instance means: it would look at a session which contains all the tutors scores (for each criteria) and then count the number of Yes for that criteria and the number of No for that criteria. Then it finds the difference between Yes and No and takes the absolute value of that number. I then added up all of these values for the different sessions we get the above list which I named total_variance. So we can see that, starting with the first criteria and increasing respectively, we have 104 more Yes than No...and so on. In this instance, whether Yes or No was more doesn't matter, it's to see the "conflict" in the decision. The smaller the number, the more conflict there was between tutors on either Yes/No for that criteria means the # of Yes was closer to the # of No. In the list total variance, the outlier here is the second to last criteria: 
    **Instruction [Consistently check in with the student to ensure that they are following along?]**

Based on this data, I would suggest reconfiguring this question to either include more details and more guidance, or looking for another question to replace it. It could be confusing because of the vagueness of "consistently checking in" and its hard to detect whether or not a student is following along because they might just respond with one word answers claiming to follow along, but at the end you realize they don't. I would propose changing this question to:
    **Does tutor guide student towards solution and ensure that each step of the process is fully explained with student feedback?**

I believe this question is more clear in that it requires the tutor to guide the student towards the solution, which clarifies consistently checking in, and making sure that each step of the process is explained with the student giving feedback before moving on.

Next, I looked to verify this result by doing a dominance comparison. I aggregated all the information on Yes/No for each session for each criteria and then whichever decision was more dominant (more chosen), I used that as the "group" decision for that criteria for the session. I will call this group decision the "average" tutor. I then compared the "average" tutor to the answer key, since I wanted to confirm the controversial criteria 7, and also see how the dominance worked out in comparison to the answer key. So I did the following 

```python
# Finds the dominant decision (yes/no) for each criteria for each session and returns all of them in a list

def dominant_finder(session):
    dominant = []
    for key, value in session.items():
        num_yes = 0
        num_no = 0
        for decision in value:
            if decision.lower() == "yes":
                num_yes += 1
            else:
                num_no += 1
        if num_yes > num_no:
            dominant.append('Yes')
        else:
            dominant.append('No')
    return dominant

dominant_dict = collections.OrderedDict()
for i in range(0, 10):
    dominant_dict["session_" + str(i+1)] = dominant_finder(session_dict_list[i])

answer_key_dict = collections.OrderedDict()
for i, item in enumerate(answer_key):
    answer_key_dict["session_" + str(i+1)] = item[3:11]

pp.pprint(dominant_dict)
pp.pprint(answer_key_dict)

dom_ans_diff = collections.OrderedDict()
for key, value in dominant_dict.items():
    diffs = []
    for i in range(8):
        if value[i] == answer_key_dict[key][i]:
            diffs.append(0)
        else:
            diffs.append(1)
    dom_ans_diff[key] = diffs

print(dom_ans_diff)

# We do two things here, we find the dominant answer for each critera for each session, then proceed to create a
# list for each session with the dominant decision which is the first dictionary called dominant_dict.
# Then we create the answer key dictionary which is simply what the answer key states should be correct for each session.
# Then we checked the differences and put them into a list, so if both are equivalent, that's a value of 0, if not equivalent
# it gets a 1. So we can see easily that the biggest difference is actually only 2, which shows the power of crowd-sourcing
# tutor decisions. Even though some may have differed greatly, over a large quantity of tutors, the differences are more or
# less weeded out. Now, we see that this correlates to our data before, which we found that the second to last criteria is troubling
# because here that's the one that constantly shows up as different between answer key and tutor.
```

    {   'session_1': ['Yes', 'No', 'Yes', 'No', 'No', 'No', 'No', 'Yes'],
        'session_2': ['Yes', 'No', 'Yes', 'No', 'No', 'Yes', 'Yes', 'Yes'],
        'session_3': ['Yes', 'No', 'Yes', 'Yes', 'Yes', 'Yes', 'Yes', 'Yes'],
        'session_4': ['Yes', 'No', 'Yes', 'Yes', 'Yes', 'Yes', 'Yes', 'Yes'],
        'session_5': ['Yes', 'No', 'Yes', 'Yes', 'Yes', 'Yes', 'Yes', 'Yes'],
        'session_6': ['Yes', 'No', 'No', 'No', 'No', 'Yes', 'No', 'Yes'],
        'session_7': ['Yes', 'No', 'Yes', 'Yes', 'Yes', 'Yes', 'Yes', 'Yes'],
        'session_8': ['No', 'No', 'Yes', 'Yes', 'Yes', 'Yes', 'No', 'Yes'],
        'session_9': ['Yes', 'No', 'Yes', 'Yes', 'Yes', 'Yes', 'Yes', 'Yes'],
        'session_10': ['Yes', 'No', 'Yes', 'Yes', 'Yes', 'Yes', 'Yes', 'Yes']}
    {   'session_1': ['Yes', 'No', 'Yes', 'Yes', 'No', 'Yes', 'No', 'Yes'],
        'session_2': ['Yes', 'No', 'Yes', 'No', 'No', 'Yes', 'No', 'No'],
        'session_3': ['Yes', 'No', 'Yes', 'Yes', 'Yes', 'Yes', 'Yes', 'Yes'],
        'session_4': ['Yes', 'No', 'Yes', 'Yes', 'Yes', 'Yes', 'Yes', 'Yes'],
        'session_5': ['Yes', 'No', 'Yes', 'Yes', 'Yes', 'Yes', 'No', 'Yes'],
        'session_6': ['Yes', 'No', 'No', 'No', 'No', 'Yes', 'No', 'Yes'],
        'session_7': ['Yes', 'No', 'Yes', 'Yes', 'Yes', 'Yes', 'No', 'Yes'],
        'session_8': ['No', 'No', 'Yes', 'Yes', 'Yes', 'Yes', 'No', 'Yes'],
        'session_9': ['Yes', 'No', 'Yes', 'Yes', 'Yes', 'Yes', 'No', 'Yes'],
        'session_10': ['Yes', 'No', 'Yes', 'Yes', 'Yes', 'Yes', 'No', 'Yes']}
    {   'session_1': [0, 0, 0, 1, 0, 1, 0, 0],
        'session_2': [0, 0, 0, 0, 0, 0, 1, 1],
        'session_3': [0, 0, 0, 0, 0, 0, 0, 0],
        'session_4': [0, 0, 0, 0, 0, 0, 0, 0],
        'session_5': [0, 0, 0, 0, 0, 0, 1, 0],
        'session_6': [0, 0, 0, 0, 0, 0, 0, 0],
        'session_7': [0, 0, 0, 0, 0, 0, 1, 0],
        'session_8': [0, 0, 0, 0, 0, 0, 0, 0],
        'session_9': [0, 0, 0, 0, 0, 0, 1, 0],
        'session_10': [0, 0, 0, 0, 0, 0, 1, 0]}

If we look at these results, we can see that in the final dictionary, which has 0's for same decisions and 1's for different decisions (between the answer key and the dominant decisions), we can see that in the 7th column for each list, there's a clear abundance of 1's in comparison to the other columns. This means that it was very common for the decision by the tutors (on average) was different from that of the answer key on the 7th criteria. However, another interesting observation here shows that the difference between the answer key and the "average" tutor is actually not much. There's supposed to be a 1 point differential for the score to be accurate, and we can see that even with column 7 often being different, the most we have is 3 -> taking in the value of two for the 6th criteria. So it's interesting to observe that the law of averages takes place here, with the more tutors assessing the same session, some of the outlier scores are just factored out. This tells us that instead of assessing each tutor separately and comparing their scores to the answer key and seeing big differences, we can take them all and then do this dominance comparison. If there's a big discrepancy between the dominant decision and the answer key, then that means that part of the criteria is most likely being misunderstood or needs to be reworked. This proves two things about the criterion, that it is relatively accurate when extended over many tutors, but also that #7 criteria needs reworking.

### Deviation of Session Scores
I examined the mean and standard deviation of the session scores for tutors to see if there was any noticeable variability. As can be seen below, to find the mean, I summed up all the scores given by the tutors to each session, then divided that by the # of scores. Then to do the standard deviation, I took each score and found the difference from the average, etc.. which gave me the values below.

```python
# Finds mean and standard deviation of a session 

def mean_finder(some_list):
    x = sum(some_list)
    y = len(some_list)
    return x/y

def stdev_finder(some_list):
    average = mean_finder(some_list)
    difference = [x - average for x in some_list]
    sq_difference = [x*x for x in difference]
    variance = sum(sq_difference)/len(some_list)
    stdev = math.sqrt(variance)
    return stdev

stat_dict = collections.OrderedDict()
for key, value in scores_dict.items():
    mean_stdev = []
    mean_stdev.append(mean_finder(value))
    mean_stdev.append(stdev_finder(value))
    stat_dict[key] = tuple(mean_stdev)

pp.pprint(stat_dict)

# generally a higher variation in more poorly performing examples... which means average/lower cases
# more controversial, which is typical. Excellent cases are generally consensus good.
# Example output of stat_dict, where first one is average, second is stdev
# OrderedDict([('session_1', (3.7142857142857144, 1.2205719636167902)),
#             ('session_2', (4.928571428571429, 1.6675167899898216)),
# compare to actual answer key.
```

    {   'session_1': (4.142857142857143, 1.5971914124998496),
        'session_2': (5.357142857142857, 2.056597149841138),
        'session_3': (8.071428571428571, 0.5933302759227196),
        'session_4': (6.642857142857143, 1.9496205805651687),
        'session_5': (7.857142857142857, 0.832993127835043),
        'session_6': (4.214285714285714, 2.0763488362498044),
        'session_7': (7.0, 1.6035674514745464),
        'session_8': (6.071428571428571, 1.0996288798814753),
        'session_9': (7.357142857142857, 0.8949974347244049),
        'session_10': (6.571428571428571, 1.3477115902938006)}
    [6, 3, 8, 8, 7, 3, 7, 6, 7, 7]

The final list is the answer key score compared to the others. As we can see, the deviation is generally larger for the sessions that score on the poorer side, which indicates a bit more divisiveness between the tutors regarding lower scores. However, there is the general trend that answer key differs about ~1 point in comparison to averages which indicates the tutors are relatively accurate. With one point difference it would mean that the tutor/answer key differed completely on one criteria due to the significance of each point. It's advisable that the scale provide varying levels of success, maybe 1-5 instead of just Yes/ as mentioned before. This would allow for more accurate scores and would provide better feedback to the tutors. This would also soften the point differentials from outliers, allowing for more accurate statistics. 

### Conferring with Tutors
Since the problem mentioned initially seemed to correspond directly with certain tutors having different scoring than the answer key, I did an average score that each tutor gave, and then checked their deviation from the answer key to determine if they were a harsher grader or to help give feedback to the senior tutors. I did this by first finding the difference between the tutor's scores and the answer key and creating a list out of that. Then took that list, and then did the rest to find the average standard deviation of the tutor from the answer key. 

```python
# This function returns a dictionary that has the tutors mapped to all their scores they gave.
# We put this into tutors_list

def score_appender():
    tutors = collections.OrderedDict()
    for i in range(14):
        scores = []
        for key, value in scores_dict.items():
            scores.append(value[i])
        tutors["tutor_" + str(i+1)] = scores
    return tutors

tutors_list = score_appender()

# Finds the difference in total score for each session between tutor and answer key 
# and maps the tutor to a list with the difference

def difference_key_tutor():
    tutors_difference = collections.OrderedDict()
    for key, value in tutors_list.items():
        diff = []
        for i in range(10):
            difference = abs(value[i] - answer_key_score[i])
            diff.append(difference)
        summation = sum(diff)
        tutors_difference["diff" + key] = diff
    return tutors_difference

tutors_diff = difference_key_tutor()
pp.pprint(tutors_diff)

# Finds stdeviation of tutors scores in general from answer key
def tutor_stdev(difference):
    sq_difference = [x * x for x in difference]
    variance = sum(sq_difference)/len(difference)
    stdev = math.sqrt(variance)
    return stdev

stdev_tutors = collections.OrderedDict()

for key, value in tutors_diff.items():
    stdev_tutors[key] = tutor_stdev(value)
    
pp.pprint(stdev_tutors)

#Identify tutors with highly different views on the definition of the criteria by 
#finding their standard deviation from the answer key
```

    {   'difftutor_1': [3, 2, 0, 1, 1, 1, 0, 1, 1, 1],
        'difftutor_2': [2, 4, 1, 0, 1, 4, 2, 0, 1, 1],
        'difftutor_3': [4, 5, 1, 1, 2, 2, 2, 1, 1, 0],
        'difftutor_4': [2, 1, 0, 1, 2, 4, 1, 0, 1, 1],
        'difftutor_5': [1, 5, 0, 0, 1, 5, 1, 0, 1, 0],
        'difftutor_6': [0, 0, 0, 2, 1, 1, 2, 0, 0, 1],
        'difftutor_7': [2, 5, 0, 1, 0, 2, 0, 0, 0, 3],
        'difftutor_8': [0, 1, 0, 1, 1, 1, 0, 1, 1, 0],
        'difftutor_9': [4, 1, 0, 2, 1, 1, 1, 0, 0, 0],
        'difftutor_10': [4, 1, 1, 8, 2, 1, 4, 3, 1, 2],
        'difftutor_11': [0, 3, 1, 0, 0, 1, 0, 0, 2, 3],
        'difftutor_12': [2, 2, 1, 1, 0, 0, 0, 0, 1, 0],
        'difftutor_13': [3, 0, 0, 1, 1, 3, 2, 1, 0, 1],
        'difftutor_14': [1, 5, 0, 0, 1, 1, 1, 2, 1, 1]}
    {   'difftutor_1': 1.378404875209022,
        'difftutor_2': 2.0976176963403033,
        'difftutor_3': 2.3874672772626644,
        'difftutor_4': 1.70293863659264,
        'difftutor_5': 2.32379000772445,
        'difftutor_6': 1.0488088481701516,
        'difftutor_7': 2.073644135332772,
        'difftutor_8': 0.7745966692414834,
        'difftutor_9': 1.5491933384829668,
        'difftutor_10': 3.420526275297414,
        'difftutor_11': 1.5491933384829668,
        'difftutor_12': 1.0488088481701516,
        'difftutor_13': 1.61245154965971,
        'difftutor_14': 1.8708286933869707}

In general, we want the tutors to differ by about 1 on average from the rubric, but clearly that's not the case here. Most of the tutors differ by 1.5 and above, and a good chunk being more than 2, with one above 3. Initially I thought maybe there would be a relationship with time, but there's no clear cut relationship here. Instead, this differential means that once again, the tutors are not understanding the rubric as it's supposed to. I believe that expanding the rubric, as mentioned before would help, but also that providing very specific goals on each of these criteria would be helpful. Like key words/phrases that are being looked for, certain methods of approach, specific way of guiding the student, and so on. With a specific criteria with specific expectations, it would help the senior tutor to more easily recognize patterns that are being looked for by the answer key while also helping the junior tutor steer their teaching a certain way that would be suitable to Yup.

## Conclusion
Through the analysis, I recognized that the general pattern was that the criteria did not seem specific enough for the senior tutors to decide consistently on a decision. The Yes/No nature of the criteria causes a lot of issues regarding what's good enough to constitute a Yes and what's not good enough so that it constitutes a No. This minimalistic approach could be improved by expanding the criteria to having more levels for each criteria and providing specific examples as to which would be considered a 1, 2, 3, 4, or 5. An example of this would be like the writing rubrics used in the IB diploma, it's very clear as to what constitutes a 1, 2, ... , 7. There's a description for each grade that encompasses all elements that would constitue each score. This type of detail could be applied to each part of the criterion. By providing such a detailed rubric, it would make it clear to junior and senior tutors what the expectations are, and would reduce variability and misinterpretation. Finally, it would be helpful to speak to senior tutors about what they would consider mediocre -> outstanding to gauge how they see the criterion and then if the views are very different from the answer key, have a discussion with the senior tutor about the misalignments. 




