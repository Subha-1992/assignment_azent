# assignment_azent
#Merge The Data Scraped From Different Sources
#Import required packages
import json
from fuzzywuzzy import process

#Open raw source files
with open('.../arizona-state-university-s1.json') as f:
    e = json.load(f)
with open('.../arizona-state-university-s2.json') as f:
    d = json.load(f)

#Cleate blank list to store different params to be used in comparison
course_name=[]# assignment_azent
#Merge The Data Scraped From Different Sources
#Import required packages
import json
from fuzzywuzzy import process

#Open raw source files
with open('.../arizona-state-university-s1.json') as f:
    s1 = json.load(f)
with open('.../arizona-state-university-s2.json') as f:
    s2 = json.load(f)

#Cleate blank list to store different params to be used in comparison
course_name=[]
course_duration = []
course_level = []
for i in range(len(s2['programs'])):
    course_name.append(s2['programs'][i]['name'])
    course_duration.append(s2['programs'][i]['length_in_months'])
    course_level.append(s2['programs'][i]['level'])

#List to store index of similar courses
index_in_s1 = []
index_in_s2 = []

#Actual checks for similarity
for i in range(len(e['programs'])):
    highest = process.extractOne(e['programs'][i]['name'], course_name)
    if highest[1]>90:
        if s1['programs'][i]['length_in_months'] == course_duration[course_name.index(highest[0])]  and \
                s1['programs'][i]['level'] == course_level[course_name.index(highest[0])]:
            index_in_s2.append(course_name.index(highest[0]))
            index_in_s1.append(i)
            if highest[1]<95:
                s1['programs'][i]['Flag']='Manual Review Required'

#Update similar courses in S1 (Addition of filed present in S2 but not in S1)
for i in range(len(index_in_s1)):
    for j in s2['programs'][index_in_s2[i]]:
        if j in s1['programs'][index_in_s1[i]]:
            pass
        else:
            s1['programs'][index_in_s1[i]][j] = s2['programs'][index_in_s2[i]][j]
    s1['programs'].remove(s1['programs'][index_in_s2[i]])

#Insert unique courses from S2 to S1
s1['programs'].extend(s2['programs'])

#Storing merged data into some other file
with open(".../arizona-state-university-s1_s2.json", "w") as outfile:
    json.dump(s1, outfile)

course_duration = []
course_level = []
for i in range(len(d['programs'])):
    course_name.append(d['programs'][i]['name'])
    course_duration.append(d['programs'][i]['length_in_months'])
    course_level.append(d['programs'][i]['level'])

#List to store index of similar courses
index_in_s1 = []
index_in_s2 = []

#Actual checks for similarity
for i in range(len(e['programs'])):
    highest = process.extractOne(e['programs'][i]['name'], course_name)
    if highest[1]>90:
        if e['programs'][i]['length_in_months'] == course_duration[course_name.index(highest[0])]  and \
                e['programs'][i]['level'] == course_level[course_name.index(highest[0])]:
            index_in_s2.append(course_name.index(highest[0]))
            index_in_s1.append(i)
            if highest[1]<95:
                e['programs'][i]['Flag']='Manual Review Required'

#Update similar courses in S1 (Addition of filed present in S2 but not in S1)
for i in range(len(index_in_s1)):
    for j in d['programs'][index_in_s2[i]]:
        if j in e['programs'][index_in_s1[i]]:
            pass
        else:
            e['programs'][index_in_s1[i]][j] = d['programs'][index_in_s2[i]][j]
    d['programs'].remove(d['programs'][index_in_s2[i]])

#Insert unique courses from S2 to S1
e['programs'].extend(d['programs'])

#Storing merged data into some other file
with open(".../arizona-state-university-s1_s2.json", "w") as outfile:
    json.dump(e, outfile)
