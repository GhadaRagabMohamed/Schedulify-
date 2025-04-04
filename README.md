# Schedulify - Exam Scheduling and Course Network Analysis

## Overview
Schedulify is a project designed to schedule exams for students while considering course conflicts, ensuring that no student is assigned to two exams at the same time. The core of the scheduling algorithm is based on network analysis, where courses are represented as nodes, and edges are drawn between courses taken by the same student.

The algorithm uses a **greedy coloring approach** to assign courses to different exam periods and considers **restricted pairs**—courses that must be scheduled separately due to students taking both courses.

## Features
- Load and process student course data from an Excel file.
- Identify and prioritize pairs of courses that must be scheduled separately.
- Generate a course relationship graph to visually represent how courses are interconnected based on shared students.
- Use greedy coloring for exam scheduling.
- Save the generated schedule in a new Excel file.

## Requirements
- Python 3.x
- Libraries:
  - pandas
  - networkx
  - matplotlib
  - seaborn

You can install the required libraries using:

 bash
pip install pandas networkx matplotlib seaborn

Code Walkthrough

Step 1: Load the Data

Schedulify loads data from an Excel file containing student-course information. It processes each department's sheet separately, handles specific column names, and cleans the data.

file_path = "studentcourses.xlsx"
xls = pd.ExcelFile(file_path)

Step 2: Prepare Data

The code ensures the student and course names are standardized (lowercased and stripped of leading/trailing spaces), removes any duplicates, and checks for missing values.

Step 3: Identify Restricted Pairs

The function get_restricted_pairs identifies pairs of courses that need to be scheduled separately. These pairs are determined by finding courses taken by the same student.

def get_restricted_pairs(df):
    student_courses = df.groupby("اسم الطالب")["اسم المقرر"].apply(list)
    pairs = []
    for courses in student_courses:
        pairs.extend(itertools.combinations(courses, 2))
    pair_counts = Counter(pairs)
    restricted_pairs = [pair for pair, count in pair_counts.most_common(10)]
    return restricted_pairs

Step 4: Create a Graph

Schedulify creates a graph where each course is a node. An edge is drawn between two courses if the same student takes both courses.

G = nx.Graph()
courses = df["اسم المقرر"].unique()
G.add_nodes_from(courses)

for student, group in df.groupby("اسم الطالب"):
    courses_taken = list(group["اسم المقرر"])
    for i in range(len(courses_taken)):
        for j in range(i + 1, len(courses_taken)):
            G.add_edge(courses_taken[i], courses_taken[j])

Step 5: Apply the Greedy Coloring Algorithm

The greedy coloring algorithm assigns a color (exam period) to each course, ensuring that courses taken by the same student are not scheduled in the same period.

coloring = nx.coloring.greedy_color(G, strategy="largest_first")

Step 6: Generate Exam Schedule

The generated schedule is formatted with the courses assigned to specific days and periods, while considering the constraints and ensuring that no restricted pairs are scheduled in the same period.

exam_days = []
current_day = []
current_period = []

for color, courses in sorted(schedule.items()):
    for course in courses:
        # Apply constraints and scheduling logic

Step 7: Visualize the Network

Schedulify visualizes the course relationship graph and the distribution of course degrees using matplotlib and seaborn.

plt.figure(figsize=(12, 8))
nx.draw(G, with_labels=True, node_color=list(coloring.values()), cmap=plt.cm.rainbow)
plt.title("Course Relationship Graph")
plt.show()

plt.figure(figsize=(10, 6))
sns.histplot(list(course_degrees.values()), bins=10, kde=True)
plt.xlabel("Course Degree")
plt.ylabel("Frequency")
plt.title("Course Degree Distribution in the Network")
plt.show()

Step 8: Save the Results

Finally, the exam schedule is saved to a new Excel file, with separate sheets for each department.

with pd.ExcelWriter("Exam_Schedule_with_periods.xlsx") as writer:
    for department, df in exam_schedules:
        df.to_excel(writer, sheet_name=department, index=False)

How to Use

1. Prepare your studentcourses.xlsx file with the student-course data in the expected format.


2. Run the Python script.


3. The script will generate a new Excel file Exam_Schedule_with_periods.xlsx with the exam schedule.



Example Output

The script will create an Excel file with a sheet for each department. Each sheet will contain the following columns:

Day: The exam day.

Period: The exam period.

Courses: The course name.

Student Count: The number of students taking the course.

Student Code: The course code.


License

This project is licensed under the MIT License - see the LICENSE file for details.
