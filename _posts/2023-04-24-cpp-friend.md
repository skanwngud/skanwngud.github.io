---
title: friend
author: yjh
math: true
categories: [CPP]
tags: [cpp]
---

## friend 선언과 사용
- A class 가 B class 를 friend 로 지정하면 B class 는 A class 의 private 변수에 접근 가능
- 그러나 friend 는 일방적이므로 B class 가 A class 를 friend 로 지정하지 않으면 A class 는 B class 의 private 에 접근 불가
- friend 는 class 의 어떤 영역 (private, public) 에서도 지정 가능
- friend 는 객체 지향 언어의 정보은닉을 저해하므로 남용하지 않는 것이 좋음
- firend 는 전역함수도 지정 가능하며 그렇게 되면 전역함수도 자신이 지정 된 클래스의 private 에 접근 가능해진다
- friend 를 지정할 때 클래스나 함수의 선언도 같이 하는 효과가 있다

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstring>

using namespace std;

class Girl;  // 클래스 선언

class Boy
{
private:
	int height;
	friend class Girl;  // Gril class 를 friend 로 선언

public:
	Boy(int len) : height(len)
	{
		// empty
	}

	void ShowYourFriendInfo(Girl &frn);  // Girl 은 아직 정의 되지 않았지만 위에서 선언을 했기 때문에 컴파일이 가능
};

class Girl
{
private:
	char phNum[20];

public:
	Girl(char * num)
	{
		strcpy(phNum, num);
	}

	void ShowYourFriendInfo(Boy &frn);
	friend class Boy;  // Boy class 를 friend 로 선언
};

void Boy::ShowYourFriendInfo(Girl &frn)
{
	cout << "Her Phone number is " << frn.phNum << endl;  // 이 함수가 실행 되기 위해선 Girl 의 phNum 변수가 필요하기 때문에 클래스보다 뒤에 정의 됨
}

void Girl::ShowYourFriendInfo(Boy &frn)
{
	cout << "His height is " << frn.height << endl;
}

int main1(void)
{
	char num[20] = "010-1235-5678";
	Boy boy(170);
	Girl girl(num);
	boy.ShowYourFriendInfo(girl);
	girl.ShowYourFriendInfo(boy);

	return 0;
}

// friend 선언은 객체 지향 언어의 정보은닉성을 해치므로 남용하지 않는 것이 좋음
```

```cpp
#include <iostream>

using namespace std;

class Point;  // class 선언만 함

class PointOP  // 함수가 실행 된 시간을 측정
{
private:
	int opcnt;

public:
	PointOP() : opcnt(0)  // private 변수를 0 으로 default 로 초기화
	{
		// empty
	}

	Point PointAdd(const Point&, const Point&);  // 위에서 선언 된 class 의 함수
	Point PointSub(const Point&, const Point&);  // 위에서 선언 된 class 의 함수

	~PointOP()
	{
		cout << "Operation times: " << opcnt << endl;  // 실행 시간
	}
};

class Point  // 선언 된 함수 정의
{
private:
	int x;
	int y;

public:
	Point(const int &xpos, const int &ypos) : x(xpos), y(ypos)
	{
		// empty
	}

	friend Point PointOP::PointAdd(const Point&, const Point&);  // PointOP class 의 함수를 friend 로 지정
	friend Point PointOP::PointSub(const Point&, const Point&);  // 동일
	friend void ShowPointPos(const Point&);  // 전역 함수를 friend 로 지정
};

Point PointOP::PointAdd(const Point& pnt1, const Point& pnt2)
{
	opcnt++;  // 함수가 호출 될 때마다 상승
	return Point(pnt1.x + pnt2.x, pnt1.y + pnt2.y);  // friend 로 지정 되었기 때문에 private 변수에 접근 가능
}

Point PointOP::PointSub(const Point& pnt1, const Point& pnt2)
{
	opcnt++;
	return Point(pnt1.x - pnt2.x, pnt1.y - pnt2.y);
}

```