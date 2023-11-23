---
titme: 객체 포인터
auhtor: yjh
math: true
categories: [CPP]
tags: [cpp]
---

## 객체 포인터

- 객체의 주소값을 저장하는 포인터 변수.
- 객체 포인터는 객체 자신뿐만 아니라 해당 객체를 직간접적으로 *상속하는 모든 객체*들을 지정할 수 있다.

```cpp
#include <iostream>

class Person
{
    public:
        void Sleep() { std::cout << "Sleep" << std::endl; }
};

class Student : public Person
{
    public:
        void Study() { std::cout << "Study" << std::endl; }
};

class PartTimeStudent : public Student
{
    public:
        void work() { std::cout << "work" << std::endl; }
};

int main(void)
{
    Person *pt1 = new Student();           // Person 을 상속하는 Student 객체 지정
    Person *pt2 = new PartTimeStudent();   // Person 을 상속하는 Student 가 상속하는 PartTimeStudent 객체 지정
    Student *pt3 = new PartTimeStudent();  // Student 가 상속하는 PartTimeStudent 개체 지정

    pt1->Sleep();
    pt2->Sleep();
    pt3->Study();

    delete pt1;
    delete pt2;
    delete pt3;

    return 0;
}
```

## 함수 오버라이딩

- 상속관계에 있는 클래스 내에서 서로 동일한 이름의 함수가 존재하는 경우, 상속한 클래스의 함수가 호출.

### 함수 오버로딩 vs 함수 오버라이딩

- 동일한 이름의 함수가 상속 관계에 있는 클래스끼리 존재하더라도 매개변수, 반환값 등이 다르다면 오버로딩이 된다.

```cpp
#include <iostream>
#include <cstring>

class Employee
{
    private:
        char name[100];

    public:
        Employee(char *name)
        {
            strcpy(this->name, name);
        }

        void ShowYourName() const
        {
            std::cout << "name: " << name << std::endl;
        }
};

class PermanentWorker : public Employee
{
    private:
        int salary;  // 월 급여

    public:
        PermanentWorker(char *name, int money)
            : Employee(name), salary(money)
        {
            // empty
        }

        int getPay() const
        {
            return salary;
        }

        void ShowSalaryInfo() const
        {
            ShowYourName();
            std::cout << "salary: " << getPay() << std::endl;
        }
};

class TemporaryWorker : public Employee
{
    private:
        int workTime;
        int payPerHour;

    public:
        TemporaryWorker(char * name, int pay)
            : Employee(name), workTime(0), payPerHour(pay)
        {
            // empty
        }

        void AddWorkTime(int time)  // 일한 시간 추가
        {
            workTime += time;
        }

        int getPay() const  // 이 달의 급여
        {
            return workTime * payPerHour;
        }

        void ShowSalaryInfo() const
        {
            ShowYourName();
            std::cout << "Salary: " << getPay() << std::endl;
        }
};

class SalseWorker : public PermanentWorker
{
    private:
        int salseResult;
        double bonusRatio;

    public:
        SalseWorker(char * name, int money, double ratio)
            : PermanentWorker(name, money), salseResult(0), bonusRatio(ratio)
        {
            // empty
        }

        void AddSalsesResult(int value)
        {
            salseResult += value;
        }

        int getPay() const
        {
            // 실제 호출 되는 것은 상속 된 PermanentWorker 의 getPay 함수이므로, SalserWorker 의 함수가 호출 되기 위해선 해당 클래스 안에도 같은 이름의 함수가 존재해야한다.
            return PermanentWorker::getPay() + (int)(salseResult * bonusRatio);
        }

        void ShowSalaryInfo() const
        {
            ShowYourName();
            std::cout << "Salary: " << getPay() << std::endl << std::endl;
        }
};

class EmployeeHandler
{
    private:
        Employee *empList[50];
        int empNum;

    public:
        EmployeeHandler() : empNum(0)
        {
            // empty
        }

        void AddEmployee(Employee *emp)
        {
            empList[empNum++] = emp;
        }

        void ShowAllSalaryInfo() const
        {
            // for (int i = 0; empNum; i++)
            // {
            //     empList[i]->ShowSalaryInfo();
            // }
        }

        void ShowTotalSaraly() const
        {
            int sum = 0;

            // for (int i = 0; i < empNum; i++)
            // {
            //     sum += empList[i]->GetPay();
            // }
            std::cout << "salary sum: " << sum << std::endl;
        }

        ~EmployeeHandler()
        {
            for (int i = 0; i < empNum; i++)
            {
                delete empList[i];
            }
        }
};

int main(void)
{
    // 직원 관리를 목적으로 설계 된 컨트롤 클래스의 객체생성
    EmployeeHandler handler;

    // 정규직 직원 등록
    handler.AddEmployee(new PermanentWorker("KIM", 1000));
    handler.AddEmployee(new PermanentWorker("LEE", 1500));

    // 임시직 직원 등록
    TemporaryWorker *alba = new TemporaryWorker("Jung", 700);
    alba->AddWorkTime(5);
    handler.AddEmployee(alba);

    // 영업직 등록
    SalseWorker *seller = new SalseWorker("Hong", 1000, 0.1);
    seller->AddSalsesResult(7000);
    handler.AddEmployee(seller);

    // 이번 달에 지불해야 할 급여의 정보
    handler.ShowAllSalaryInfo();

    // 이번 달에 지불해야 할 급여의 총합
    handler.ShowTotalSaraly();

    return 0;
}
```
