title: OOP BY C LANGUAGE
date: 2012-07-12 10:44:27
tags: [OOP,C]
---

这几天又仔细研究了下 C OOP ,有点新的结果,在这分享下,下面大概说下.

#### Inherit(继承)  

```C
#define CB_CLASS_INHERIT_BEGIN(THIS, PARENT_CLASS) \
	struct THIS	\
{	\
	PARENT_CLASS super;	\

#define CB_CLASS_INHERIT_END()	\
};	\

#define CB_CLASS_POINTER_SET_PARENT(THIS, name, value)	\
	((THIS)->super)->name = value;

#define CB_CLASS_SET_PARENT(THIS, name, value) \
	((THIS).super).name = value;

typedef struct _Base Base;
struct _Base {
	int a;
};

typedef struct _Child Child;

CB_CLASS_INHERIT_BEGIN(_Child, Base) /**< _Child继承Base结构体 */
		int b;
		int c;
		CB_CLASS_INHERIT_END()

		int main(int argc, char *argv[])
{
	Child m;
	CB_CLASS_SET_PARENT(m, a, 5);
	m.b = 3;
	printf("Parent a=%d,Child b=%d\n",m.super.a, m.b);
	return 0;
}
```

####Template(模板)

```C
/* — Struct Template — */
#define TplTest(T)	\
		struct _test {	\
			T a;	\
		};

/* — Function Template — */
#define TplTestFun(T)	\
	T testFun() {	\
		T a = 0;	\
		return a;	\
	}

int main(int argc, char *argv[])
{
	TplTest(char*);
	struct _test obj;
	obj.a="This is a String";
	TplTestFun(int);
	printf("%s,%d\n", obj.a, testFun());
	return 0;
}
```

####Polymorphism(多态)
C多态机制主要是实现类似C++的虚函数表

```C
#include <Akita/Akita.h>
typedef struct _Base Base;
typedef struct _BaseVt BaseVt;
typedef struct _Child Child;
typedef struct _ChildVt ChildVt;
#define GetVt(p,type) ((type*)(void*)p)->vt
#define BaseShow(p) GetVt(p,BaseVt).doSomething((Base*)p);
#define ChildShow(p) GetVt(p,ChildVt).doAnthing((Child*)p);
void displayBase();
void displayChild();
void justDoIt();
/* — Base — */
struct _Base {
	void (*doSomething)(Base* p); /**< Function Pointer */
};
struct _BaseVt {
	Base vt;
};
/* — Child — */
CB_CLASS_INHERIT_BEGIN(_Child, Base); /< Inherit */
void (*doAnthing)(Child* p);
CB_CLASS_INHERIT_END();
struct _ChildVt {
	Child vt;
};
/* — Base Init — */
void InitBase(BaseVt **p) {
	*p = (BaseVt*)malloc(sizeof(BaseVt));
	(*p)->vt.doSomething = displayBase;
}
/* — Child Init — */
void InitChild(ChildVt **p) {
	*p = (ChildVt*)malloc(sizeof(ChildVt));
	(*p)->vt.super.doSomething = displayChild;
	(*p)->vt.doAnthing = justDoIt;
}
void displayBase() {
	printf("%s\n", "I’m Base");
}
void displayChild() {
	printf("%s\n", "I’m Child");
}
void justDoIt() {
	printf("%s\n", "I’m Child ,do anything");
}
int main(int argc, char *argv[])
{
	BaseVt* p;
	InitBase(&p);
	BaseShow(p);

	InitChild((Child **)&p);
	BaseShow(p);
	ChildShow(p);

	return 0;
}
```

(PS:同样是12年自己写得一篇文章,保留在新博客)

