

这段Java代码实现了一个简单的学生成绩管理系统。代码主要分为几个模块，包括角色类（`Role`）、文件输入输出类（`RoleIO`），以及主管理系统类（`Manage_System`）。下面逐模块逐行进行解释。

### 1. `Role` 类

```java
class Role {
    public List<Role> studentGrades;
```
`Role`类用于存储学生的信息和成绩。`studentGrades`是一个`List<Role>`，用于存储学生的成绩信息。

```java
    public String student_name;
    public String student_Id;
    public String course_name;
    public String course_Id;
    public double credit;
    public double score;
    public String score_grade;
```
定义了一些属性来存储学生姓名、学号、课程名称、课程号、学分、成绩以及成绩等级。

```java
    public List<Role> getStudentGrades() {
        return studentGrades;
    }
    public Role() {
        studentGrades = new ArrayList<>();
    }
```
`getStudentGrades`方法返回学生的成绩列表。构造函数初始化`studentGrades`为一个空的ArrayList。

#### 计算GPA方法

```java
    public double calculateGPA(String studentId) {
        double totalPoints = 0.0;
        double totalCredits = 0.0;
```
`calculateGPA`方法接收一个学号，计算该学生的总GPA。

```java
        for (Role grade : studentGrades) {
            if (grade.student_Id.equals(studentId)) {
                double gpa = grade.getGPA();
                totalPoints += gpa * grade.credit;
                totalCredits += grade.credit;
            }
        }
```
遍历学生成绩列表，如果当前成绩的学生ID与输入的相同，则计算GPA和学分的总和。

```java
        return totalPoints / totalCredits;
    }
```
最后返回计算结果。

#### 添加学生成绩信息的方法

```java
    public void addStudentGrade(String studentName, String studentId, String courseName, String courseId, double credit, double score) {
```
`addStudentGrade`方法用于添加学生的成绩信息。

```java
        Role role = new Role();
        role.student_name = studentName;
        role.student_Id = studentId;
        role.course_name = courseName;
        role.course_Id = courseId;
        role.credit = credit;
        role.score = score;
        role.score_grade = calculateGrade(score);
        studentGrades.add(role);
    }
```
创建一个新的`Role`对象，将输入的成绩信息填充，然后添加到`studentGrades`列表中。

#### 计算成绩等级的方法

```java
    public String calculateGrade(double score) {
```
此方法用于计算成绩等级。

```java
        String[] grades = {"A", "A-", "B+", "B", "B-", "C+", "C", "C-", "D+", "D"};
        double[] scores = {90, 87, 83, 80, 77, 73, 70, 67, 63, 60};
```
定义了成绩等级和对应的分数。

```java
        for (int i = 0; i < grades.length; i++) {
            if (score >= scores[i]) {
                return grades[i];
            }
        }
        return "F";
    }
```
根据输入的分数返回对应的等级，如果分数在所有等级中都不达标则返回"F"。

#### 获取GPA的方法

```java
    public double getGPA() {
```
此方法用于获取某个成绩的GPA值。

```java
        for (int i = 0; i < grades.length; i++) {
            if (score_grade.equals(grades[i])) {
                return gpas[i];
            }
        }
        return 0.0;
    }
}
```
根据成绩等级返回对应的GPA值。

### 2. `RoleIO` 类

```java
class RoleIO {
    private Role role;

    public RoleIO(Role role) {
        this.role = role;
    }
```
这个类负责处理输入和输出操作，构造函数接收一个`Role`对象，用于对学生信息进行操作。

#### 执行职责的方法

```java
    public void performDuty(Scanner input) {
        int choice = 0;
        while (choice != 5) {
```
开始一个循环，让用户选择功能，直到选择退出。

```java
            System.out.println("请选择功能：\n" +
                    "1. 单个学生成绩录入 \n" +
                    ...
            choice = input.nextInt();
```
输出菜单并读取用户输入。

#### 输入成绩

```java
    public void inputGrade(Scanner input) {
```
这个方法用于输入单个学生的成绩信息。

```java
        System.out.println("请输入学生姓名:");
        String studentName = input.nextLine();
        ...
        role.addStudentGrade(studentName, studentId, courseName, courseId, credit, score);
    }
```
读取用户输入的信息，并调用`addStudentGrade`方法将其添加到学生成绩列表中。

#### 从CSV文件导入成绩

```java
    public void importGradesFromCSV(String filename) {
```
实现了从CSV文件导入成绩的功能。

```java
        try (BufferedReader reader = new BufferedReader(
                new InputStreamReader(new FileInputStream(filename), Charset.forName("UTF-8")))) {
            String line;
            boolean isFirstLine = true;
```
使用BufferedReader读取CSV文件内容，并跳过第一行标题。

```java
            while ((line = reader.readLine()) != null) {
                if (isFirstLine) {
                    isFirstLine = false; // 跳过标题行
                    continue;
                }
                String[] parts = line.split(",");
                ...
                role.addStudentGrade(studentName, studentId, courseName, courseId, credit, score);
            }
```
读取每一行并分割成数组元素，确保数组长度为6（记录完整的成绩信息），然后调用`addStudentGrade`添加到列表中。

#### 保存成绩到TXT文件的方法

```java
    public void saveGradesToTXT(String filePath) {
```
将学生信息保存到一个TXT文件中。

```java
        try (BufferedWriter writer = new BufferedWriter(new FileWriter(filePath))) {
            Set<String> processedIds = new HashSet<>();
            ...
        }
```
使用BufferedWriter写入文件，避免重复记录同一个学生的信息，利用Set来存储已处理的学生ID。

```java
            for (Role grade : role.studentGrades) {
                if (!processedIds.contains(grade.student_Id)) {
                    ...
                }
            }
```
循环遍历学生成绩，将每个学生的详细信息写入文件中。

#### 输出所有学生GPA的方法

```java
    public void outputAllStudentGPAs() {
```
此方法用于输出所有学生的GPA。

```java
        for (Role grade : role.studentGrades) {
            if (!printedStudentIds.contains(grade.student_Id)) {
                double totalGPA = role.calculateGPA(grade.student_Id);
                ...
            }
        }
```
遍历学生成绩列表，计算并输出每个学生的总GPA。

### 3. `Manage_System` 类

```java
public class Manage_System {
    public static void main(String[] args) {
        Scanner input = new Scanner(System.in);
        Role role = new Role();
        RoleIO roleIO = new RoleIO(role);
        ...
    }
}
```
主类用于启动程序，创建Scanner对象接收用户输入，创建`Role`和`RoleIO`对象，并调用`performDuty`方法处理用户输入。

这段程序的整体架构相对清晰，通过简单的类与方法分工，实现了对学生成绩的管理功能，包括输入、导入、保存和输出。通过对每一个模块的功能化处理，使得代码的可读性和可维护性得到了增强。
