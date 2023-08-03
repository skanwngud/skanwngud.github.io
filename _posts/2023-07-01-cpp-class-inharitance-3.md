---
title: 클래스 상속 3
author: yjh
math: true
categories: [CPP]
tags: [cpp]
---

## 클래스 상속을 위한 조건
### IS-A 관계 성립
- ~는 ~이다.
- Super class 와 sub class 간에 IS-A 관계가 성립해야한다.

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstring>

class Computer
{
private:
	char owner[50];

public:
	Computer(const char * name)
	{
		strcpy(owner, name);
	}

	void Calculate()
	{
		std::cout << "요청 내용을 계산합니다." << std::endl;
	}
};

class NoteBookComp : Computer
{
private:
	int Battery;

public:
	NoteBookComp(const char * name, int initChag)
		: Computer(name), Battery(initChag)
	{
		// empty
	}

	void Charging() { Battery += 5; }
	void UseBattery() { Battery -= 1; }
	void MovingCal()
	{
		if (GetBatteryInfo() < 1)
		{
			std::cout << "충전이 필요합니다." << std::endl;
			return;
		}

		std::cout << "이동하면서 ";
		Calculate();
		UseBattery();
	}

	int GetBatteryInfo() { return Battery; }
};

class TabletNoteBook : public NoteBookComp
{
private:
	char regstrPenModel[50];

public:
	TabletNoteBook(const char * name, int initChag, const char * pen)  // NoteBookComp 와 Computer 의 생성자 둘 다 생성
		: NoteBookComp(name, initChag)
	{
		strcpy(regstrPenModel, pen);
	}

	void Write(const char * penInfo)
	{
		if (GetBatteryInfo() < 1)
		{
			std::cout << "충전이 필요합니다." << std::endl;
			return;
		}

		if (strcmp(regstrPenModel, penInfo) != 0)
		{
			std::cout << "등록 된 펜이 아닙니다." << std::endl;
			return;
		}

		std::cout << "필기 내용을 처리합니다." << std::endl;
		UseBattery();
	}
};

int main(void)
{
	NoteBookComp nc("가나다", 5);
	TabletNoteBook tn("다라마", 5, "ISE-241-242");

	nc.MovingCal();
	tn.Write("ISE-241-242");

	return 0;
}

// TabletNoteBook is NoteBookComp
// NoteBookComp is Computer 
// Also, TabletNoteBook is Computer 라는 관계 성립
```

### HAS-A 관계
- ~는 ~를 포함한다.
- Sub class 는 super class 가 지니는 모든 것을 소유한다.
- HAS-A 관계는 상속의 조건이 되지만 복합 관계로 이를 대신하는 것이 일반적이다.

```cpp
// HAS-A
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstring>

class Gun
{
private:
	int bullet;

public:
	Gun(int bnum) : bullet(bnum)
	{
		// empty
	}

	void Shot()
	{
		std::cout << "BBANG!" << std::endl;
		bullet--;
	}
};

class Police : public Gun  // Police 라는 클래스가 Gun 을 갖고있음
{
private:
	int handcuffs;

public:
	Police(int bnum, int bcuff)
		: Gun(bnum), handcuffs(bcuff)
	{
		// empty
	}

	void PutHandCuff()
	{
		std::cout << "SNAP!" << std::endl;
		handcuffs--;
	}
};

int main(void)
{
	Police pman(5, 3);
	pman.Shot();
	pman.PutHandCuff();

	return 0;
}
```

- 위의 예제를 다른 방식으로 표현 가능.
```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstring>

class Gun
{
private:
	int bullet;

public:
	Gun(int bnum)
		: bullet(bnum)
	{
		// empty
	}

	void Shot()
	{
		std::cout << "BBANG!" << std::endl;
		bullet--;
	}
};

class Police
{
private:
	int handcuffs;
	Gun * pistol;  // 클래스 포인터, 상속이 아니라 객체를 생성해서 참조

public:
	Police(int bnum, int bcuff)
		:handcuffs(bcuff)
	{
		if (bnum > 0)  // bnum 이 1 이상이면 총에 장전
			pistol = new Gun(bnum);

		else  // 0 이면 총에 아무것도 장전하지 않음
			pistol = NULL;
	}

	void PutHandcuff()
	{
		std::cout << "SNAP!" << std::endl;
		handcuffs--;
	}

	void Shot()  // Gun 클래스를 상속한다면 필요없지만 객체를 만들어 참조하기 때문에 필요
	{
		if (pistol == NULL)
			std::cout << "Hut BBANG" << std::endl;

		else
			pistol->Shot();  // 객체로 생성 된 Gun 클래스의 함수 호출
	}

	~Police()
	{
		if (pistol == NULL)
			delete pistol;
	}
};

int main(void)
{
	Police pman1(5, 3);
	pman1.Shot();
	pman1.PutHandcuff();

	Police pman2(0, 3);
	pman2.Shot();
	pman2.PutHandcuff();

	return 0;
}

// HAS-A 관계는 총을 소유하지 않은 경찰을 표현하기 어려우나 Composite 는 NULL 로 초기화해 간단히 해결.
// 상속으로 서로 묶인 HAS 관계에서는 클래스를 확장하는 것도 쉽지 않다.
```