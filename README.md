# pandas-challenge

# Dependency/Setup
import pandas as pd

# File to Load
school_data_to_load = "Downloads/Starter_Code/PyCitySchools/Resources/schools_complete.csv"
student_data_to_load = "Downloads/Starter_Code/PyCitySchools/Resources/students_complete.csv"

# Read data files/Store into Pandas DataFrames
school_data = pd.read_csv(school_data_to_load)
student_data = pd.read_csv(student_data_to_load)

# Combine data into a single dataset.  
school_data_complete = pd.merge(student_data, school_data, how="left", on=["school_name", "school_name"])
school_data_complete.head()

# Calculate the total number of unique schools
school_count = school_data_complete['school_name'].unique()
total_schools = len(school_count)
total_schools

# Calculate the total number of students
student_count = school_data_complete['student_name'].count()
student_count

# Calculate the total budget
total_budget = school_data["budget"].sum()
total_budget

# Calculate the average (mean) math score
average_math_score = school_data_complete["math_score"].mean()
average_math_score

# Calculate the average (mean) reading score
average_reading_score = school_data_complete['reading_score'].mean()
average_reading_score

# Use the following to calculate the percentage of students who passed math (math scores greather than or equal to 70)
passing_math_count = school_data_complete[(school_data_complete["math_score"] >= 70)].count()["student_name"]
passing_math_percentage = passing_math_count / float(student_count) * 100
passing_math_percentage

# Calculate the percentage of students who passeed reading   
passing_reading_count = school_data_complete[(school_data_complete["reading_score"] >= 70)].count()["student_name"]
passing_reading_percentage = passing_reading_count / float(student_count) * 100
passing_reading_percentage

# Use the following to calculate the percentage of students that passed math and reading
passing_math_reading_count = school_data_complete[
    (school_data_complete["math_score"] >= 70) & (school_data_complete["reading_score"] >= 70)
].count()["student_name"]
overall_passing_rate = passing_math_reading_count /  float(student_count) * 100
overall_passing_rate

# Create a high-level snapshot of the district's key metrics in a DataFrame
district_summary = pd.DataFrame({"Total Schools": total_schools,"Total Students": f"{student_count:,}","Total Budget": f"${total_budget:,.2f}", "Average Math Score": f"{average_math_score:.6f}","Average Reading Score": f"{average_reading_score:.5f}","% Passing Math": f"{passing_math_percentage:.6f}","% Passing Reading": f"{passing_reading_percentage:.6f}","% Overall Passing": f"{overall_passing_rate: .6f}"}, index=[0])

#Display the DataFrame
district_summary 

# Group by school name
school_name = school_data_complete.set_index('school_name').groupby(['school_name'])

# Use the code provided to select the school type
school_types = school_data.set_index('school_name')['type']

# Calculate the total student count
per_school_counts = school_name['Student ID'].count()

# Calculate the total school budget and per capita spending
per_school_budget = school_data_complete.groupby(["school_name"]).mean()["budget"]
per_school_capita = per_school_budget / per_school_counts

# Calculate the average test scores
per_school_math = school_name['math_score'].mean()
per_school_reading = school_name['reading_score'].mean()

# Calculate the number of schools with math scores of 70 or higher
school_passing_math = school_data_complete[school_data_complete['math_score'] >= 70].groupby('school_name')['Student ID'].count()

# Calculate the number of schools with reading scores of 70 or higher
school_passing_reading = school_data_complete[school_data_complete['reading_score'] >= 70].groupby('school_name')['Student ID'].count()

# Use the provided code to calculate the schools that passed both math and reading with scores of 70 or higher
passing_math_and_reading = school_data_complete[(school_data_complete["reading_score"] >= 70) & (school_data_complete["math_score"] >= 70)]

# % Overall Passing Rates
overall_passing_rate = school_data_complete[(school_data_complete['reading_score'] >= 70) & (school_data_complete['math_score'] >= 70)].groupby('school_name')['Student ID'].count()/per_school_counts*100


# Create a DataFrame called `per_school_summary` with columns for the calculations above.
per_school_summary = pd.DataFrame({
    "School Type": school_types,
    "Total Students": per_school_counts,
    "Per Student Budget": per_school_budget,
    "Total School Budget": per_school_capita,
    "Average Math Score": per_school_math,
    "Average Reading Score": per_school_reading,
    '% Passing Math': school_passing_math,
    '% Passing Reading': school_passing_reading,
    "% Overall Passing": overall_passing_rate
})


#Formatting
per_school_summary = school_summary[['School Type', 
                          'Total Students', 
                          'Total School Budget', 
                          'Per Student Budget', 
                          'Average Math Score', 
                          'Average Reading Score',
                          '% Passing Math',
                          '% Passing Reading',
                          '% Overall Passing']]


#Display DataFrame
per_school_summary.style.format({'Total Students': '{:}',
                          "Total School Budget": "${:,.2f}",
                          "Per Student Budget": "${:.2f}",
                          'Average Math Score': "{:6f}", 
                          'Average Reading Score': "{:6f}", 
                          "% Passing Math": "{:6f}", 
                          "% Passing Reading": "{:6f}"})

# Sort the schools by `% Overall Passing` in descending order and display the top 5 rows.

top_perform = school_summary.sort_values("% Overall Passing", ascending = False)
top_perform.head().style.format({'Total Students': '{:}',
                           "Total School Budget": "${:,.2f}", 
                           "Per Student Budget": "${:.2f}", 
                           "% Passing Math": "{:6f}", 
                           "% Passing Reading": "{:6f}", 
                           "% Overall Passing": "{:6f}"})

# Sort the schools by `% Overall Passing` in ascending order and display the top 5 rows.
bottom_perform = top_perform.tail()
bottom_perform = bottom_perform.sort_values('% Overall Passing')
bottom_perform.style.format({'Total Students': '{: }', 
                       "Total School Budget": "${:,.2f}", 
                       "Per Student Budget": "${:.2f}", 
                       "% Passing Math": "{:6f}", 
                       "% Passing Reading": "{:6f}", 
                       "% Overall Passing": "{:6f}"})

# Separate the data by grade and group by "school_name" and take the mean of each.
ninth_math = student_data.loc[student_data['grade'] == '9th'].groupby('school_name')["math_score"].mean()
tenth_math = student_data.loc[student_data['grade'] == '10th'].groupby('school_name')["math_score"].mean()
eleventh_math = student_data.loc[student_data['grade'] == '11th'].groupby('school_name')["math_score"].mean()
twelfth_math = student_data.loc[student_data['grade'] == '12th'].groupby('school_name')["math_score"].mean()

# Select only the `math_scores`
math_scores = pd.DataFrame({
        "9th": ninth_math,
        "10th": tenth_math,
        "11th": eleventh_math,
        "12th": twelfth_math
})
math_scores = math_scores[['9th', '10th', '11th', '12th']]
math_scores.index.name = "School Name"

# Display DataFrame
math_scores.style.format({'9th': '{:.6f}', 
                          "10th": '{:.6f}', 
                          "11th": "{:.6f}", 
                          "12th": "{:.6f}"})

# Separate the data by grade and group by "school_name" and take the mean of each.
ninth_reading = student_data.loc[student_data['grade'] == '9th'].groupby('school_name')["reading_score"].mean()
tenth_reading = student_data.loc[student_data['grade'] == '10th'].groupby('school_name')["reading_score"].mean()
eleventh_reading = student_data.loc[student_data['grade'] == '11th'].groupby('school_name')["reading_score"].mean()
twelfth_reading = student_data.loc[student_data['grade'] == '12th'].groupby('school_name')["reading_score"].mean()

# Select only the `reading_scores`
reading_scores = pd.DataFrame({
        "9th": ninth_reading,
        "10th": tenth_reading,
        "11th": eleventh_reading,
        "12th": twelfth_reading
})
reading_scores = reading_scores[['9th', '10th', '11th', '12th']]
reading_scores.index.name = "School Name"


# Display DataFrame
reading_scores.style.format({'9th': '{:.6f}', 
                             "10th": '{:.6f}', 
                             "11th": "{:.6f}", 
                             "12th": "{:.6f}"})

# Establish the bins 
spending_bins = [0, 585, 630, 645, 680]
labels = ["<$585", "$585-630", "$630-645", "$645-680"]

# Create a copy of the school summary since it has the "Per Student Budget" 
school_spending_df = per_school_summary.copy()

# Use `pd.cut` to categorize spending based on the bins.
school_data_complete['spending_bins'] = pd.cut(school_data_complete['budget']/school_data_complete['size'], spending_bins, labels)

school_spending_df = school_data_complete.groupby('spending_bins')


avg_math = school_spending_df['math_score'].mean()
avg_read = school_spending_df['reading_score'].mean()

pass_math = school_data_complete[school_data_complete['math_score'] >= 70].groupby('spending_bins')['Student ID'].count()/school_spending_df['Student ID'].count()*100
pass_read = school_data_complete[school_data_complete['reading_score'] >= 70].groupby('spending_bins')['Student ID'].count()/school_spending_df['Student ID'].count()*100
overall = school_data_complete[(school_data_complete['reading_score'] >= 70) & (school_data_complete['math_score'] >= 70)].groupby('spending_bins')['Student ID'].count()/school_spending_df['Student ID'].count()*100

# DataFrame           
spending_summary = pd.DataFrame({
    "Average Math Score": avg_math,
    "Average Reading Score": avg_read,
    "% Passing Math": pass_math,
    "% Passing Reading": pass_read,
    "% Overall Passing": overall
            
})
            
# Columns
spending_summary = spending_summary[[
    "Average Math Score",
    "Average Reading Score",
    "% Passing Math",
    "% Passing Reading",
    "% Overall Passing"
]]

spending_summary.index.name = "Per Student Budget"

# Format
spending_summary.style.format({'Average Math Score': '{:.2f}', 
                              'Average Reading Score': '{:.2f}', 
                              '% Passing Math': '{:.2f}', 
                              '% Passing Reading':'{:.2f}', 
                              '% Overall Passing': '{:.2f}'})

# create size bins
bins = [0, 1000, 2000,5000]
labels = ["Small (<1000)", "Medium (1000-2000)" , "Large (2000-5000)"]

# Group by size
by_size = school_data_complete.groupby('size_bins')

# Use `pd.cut` on the "Total Students" column of the `per_school_summary` DataFrame.
school_data_complete['size_bins'] = pd.cut(school_data_complete['size'], bins, labels = labels)


# Calculate averages for the desired columns. 
average_math_score = by_size['math_score'].mean()
average_reading_score = by_size['math_score'].mean()
pass_math_percent = school_data_complete[school_data_complete['math_score'] >= 70].groupby('size_bins')['Student ID'].count()/by_size['Student ID'].count()*100
pass_read_percent = school_data_complete[school_data_complete['reading_score'] >= 70].groupby('size_bins')['Student ID'].count()/by_size['Student ID'].count()*100
overall = school_data_complete[(school_data_complete['reading_score'] >= 70) & (school_data_complete['math_score'] >= 70)].groupby('size_bins')['Student ID'].count()/by_size['Student ID'].count()*100

            
# DataFrame            
size_summary = pd.DataFrame({
    "Average Math Score": average_math_score,
    "Average Reading Score": average_reading_score,
    '% Passing Math': pass_math_percent,
    '% Passing Reading': pass_read_percent,
    '% Overall Passing': overall
            
})
            
# Columns
size_summary = size_summary[[
    "Average Math Score",
    "Average Reading Score",
    '% Passing Math',
    '% Passing Reading',
    '% Overall Passing'
]]

size_summary.index.name = "Total Students"
size_summary = size_summary.reindex(labels)

# Format
size_summary.style.format({'Average Math Score': '{:.6f}', 
                              'Average Reading Score': '{:.6f}', 
                              '% Passing Math': '{:.6f}', 
                              '% Passing Reading':'{:.6f}', 
                              '% Overall Passing': '{:.6f}'})

# Group by type of school
school_type = school_data_complete.groupby("type")

# Average of Results
average_math_score = school_type['math_score'].mean()
average_reading_score = school_type['math_score'].mean()
pass_math_percent = school_data_complete[school_data_complete['math_score'] >= 70].groupby('type')['Student ID'].count()/school_type['Student ID'].count()*100
pass_read_percent = school_data_complete[school_data_complete['reading_score'] >= 70].groupby('type')['Student ID'].count()/school_type['Student ID'].count()*100
overall = school_data_complete[(school_data_complete['reading_score'] >= 70) & (school_data_complete['math_score'] >= 70)].groupby('type')['Student ID'].count()/school_type['Student ID'].count()*100

# Display DataFrame           
type_summary = pd.DataFrame({
    "Average Math Score": average_math_score,
    "Average Reading Score": average_reading_score,
    '% Passing Math': pass_math_percent,
    '% Passing Reading': pass_read_percent,
    "% Overall Passing": overall})
    
# Column Data
type_summary = type_summary[[
    "Average Math Score",
    "Average Reading Score",
    '% Passing Math',
    '% Passing Reading',
    "% Overall Passing"
]]
type_summary.index.name = "Type of School"


#formating
type_summary.style.format({'Average Math Score': '{:.6f}', 
                              'Average Reading Score': '{:.6f}', 
                              '% Passing Math': '{:.6f}', 
                              '% Passing Reading':'{:.6f}', 
                              '% Overall Passing': '{:.6f}'})


