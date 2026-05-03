# POST 请求，插入新学生
@dbapp.post("/newstudent")
def newstudent(name: str, course: str) -> dict:
cursor = database_creds.cursor()
sql = "INSERT INTO student (name, course) VALUES (%s, %s)"
val = (name, course)
cursor.execute(sql, val)
database_creds.commit()
return {"message": "student added successfully"}
```

这里我们展示了一个非常直接的示例，演示了 FastAPI 如何与 MySQL 数据库实例交互。我们创建了带有特定 SQL 查询的函数，使用适当的 FastAPI 方法对函数进行了装饰，并在需要的地方传递了参数。让我们看看 FastAPI 与给定数据库实例交互的另一种方法。

对象关系映射
对象关系映射是一种编程概念，用于在面向对象编程代码和关系型数据库之间建立桥梁。顾名思义，它将代码中的对象“映射”到关系型数据库对象。最常见的情况是，你在代码中创建一个类，该类代表关系型数据库中的一个表。表的字段名称及其相应类型由类的属性表示。最后，这些对象的每个实例都代表关系型数据库中表的一行。ORM 库简化了 Python 脚本与关系型数据库之间的交互。使用 ORM 的优势在于，它旨在消除任何 SQL 注入攻击的可能性。
一些更常用的 ORM 库包括 Peewee、SQLAlchemy 和 Django ORM。Django 作为一个 Web 框架，内置了自己的 ORM 库。目前，采用最广泛或使用最多的 ORM 库是 SQLAlchemy。

SQLAlchemy
SQLAlchemy 由两个主要模块组成：核心模块和 ORM 模块。核心模块提供了一种 SQL 抽象，能够实现与各种数据库应用程序编程接口（也称为 DBAPI）的顺畅交互。核心模块不需要采用 ORM 即可连接和与数据库交互。ORM 模块构建在核心模块之上，为 SQLAlchemy 提供对象关系映射功能。让我们看看 SQLAlchemy 中的一些关键概念。

引擎
要使用 SQLAlchemy 连接任何数据库，你需要熟悉引擎和会话这两个概念。为了连接数据库，你需要创建一个引擎。引擎是 SQLAlchemy 应用程序尝试连接并与数据库通信的起点。`create_engine` 对象提供了一种创建新数据库连接的方法。

以下是连接 PostgreSQL 数据库的示例：

```
from sqlalchemy import URL
databaseURL = URL.create(
"mysql+mysqlconnect",
username="your_username_here",
password="your_password_here",
host="localhost",
database="testdatabase",
)
engine = create_engine(databaseURL)
```

会话

另一个概念是会话。顾名思义，会话在建立连接后，会与数据库建立“对话”。会话为你的所有查询和事务（提交、回滚等）创建工作空间。如果你正在构建一个较大的应用程序，建议使用 `sessionmaker` 类来创建会话。
以下是关于如何使用“sessionmaker”创建新会话的示例：

```
from sqlalchemy import URL
from sqlalchemy.orm import sessionmaker
databaseURL = URL.create(
"mysql+mysqlconnect",
username="your_username_here",
password="your_password_here",
host="localhost",
database="testdatabase",
)
engine = create_engine(databaseURL)
sampleSession = sessionmaker(autoflush=False, bind=engine)
```


# 此处进行查询和事务处理
sampleSession.close()
```

参数“bind”是将引擎与会话关联的一种方式。
参数“autoflush”会在调用查询以向数据库发送 SQL 语句时，或在使用会话类的某些方法时，自动刷新或提交数据库事务。

查询 API
SQLAlchemy 还提供了一个查询 API，支持执行各种操作以辅助数据检索和操作。这些方法包括但不限于排序、过滤、连接、从表中获取数据等。让我们看一些 SQL 到 ORM 的示例。

假设我们在关系数据库中有一个名为“students”的表。以下是 SQL 查询的转换方式：

```
Select * from Students
```

将转换为

```
Students.select()
```

让我们看一个稍微详细的查询，我们选择检索在 2024 年秋季选修了 Chemistry 101 课程的学生：

```
Select
*
from
students
where
term = "Fall2024" and
course = "Chemistry101" and
plan = "something"
fetch first 25 rows only
```

将转换为

```
students \
.select() \
.filter( \
students.has( \
term="Fall2024", \
course="Chemistry101", \
plan="something" \
)
).fetchmany(25)
```

Alembic
此外，了解数据库迁移和 Alembic 库也很重要。数据库迁移并不一定意味着在数据库之间移动大量数据。相反，数据库迁移是关于创建和维护与应用程序相关的当前及不断演进的模式。Alembic 是一个 Python 库，用于帮助在 SQLAlchemy 应用程序中管理和维护这些不断演进的模式。

构建 REST 数据 API
让我们利用 FastAPI 和 SQLAlchemy 构建一个 RESTful 数据 API，以数据即服务（DaaS）的形式交付数据。数据即服务是一种商业概念和模型，描述按需交付高质量数据，无论提供者和消费者在地理上相隔多远。在此示例中，我们还将整合数据验证库 Pydantic，以说明在构建 RESTful 数据 API 时如何验证数据并将其发送到下游。我们利用 RESTful 架构来确保无状态性、可扩展性和通过 Web 交付数据的简洁性。

首先，让我们看看如何使用 SQLAlchemy 初始化数据库连接：

```
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker
SQLAlchemy_DB_URL = "mysql+mysqlconnector://newscott:Tiger$$$!!!9999@localhost/stu"
engine = create_engine(SQLAlchemy_DB_URL)
SessionLocal = sessionmaker(autocommit=False,
autoflush=False,
bind=engine)
Base = declarative_base()
```

接下来，我们将定义与数据库中相应表对应的类：

```
from sqlalchemy import Column, Integer, String
from database import Base
class Student(Base):
__tablename__ = "students"
id = Column(Integer, primary_key=True)
name = Column(String, unique=True, index=False)
course = Column(String, unique=False, index=True)
```

随后将使用 Pydantic 库创建一个数据验证类。代码如下所示：

```
import pydantic as pyd
class NewStudent(pyd.BaseModel):
id: int
name: str
course: str
```

现在让我们开发各种将成为 API 功能的函数。在此情况下，我们可以获取所有学生的列表、按姓名或 ID 获取学生详情，或创建新学生并提交事务：

```
from sqlalchemy.orm import Session
import model, pydanticSchema
def get_AllStudents(db: Session, offset: int = 0, limit: int = 10):
return db.query(model.Student).offset(offset).limit(limit).all()
def get_student_by_name(db: Session, name: str):
return db.query(model.Student).filter(model.Student.name == name).first()
def get_student_by_id(db: Session, id: int):
return db.query(model.Student).filter(model.Student.id == id).first()
def new_student(db: Session, student: pydanticSchema.NewStudent):
#id = 随机数生成
db_user = model.Student(id = id, name = model.Student.name, email=model.Student.email)
db.add(db_user)
db.commit()
db.refresh(db_user)
```

当我们拥有所有功能和组件后，现在来创建 FastAPI 应用程序：

```
from fastapi import Depends, FastAPI, HTTPException
from sqlalchemy.orm import Session
from database import SessionLocal, engine
import model
from crud import get_AllStudents, get_student_by_name, get_student_by_id, new_student
from pydanticSchema import NewStudent
model.Base.metadata.create_all(bind=engine)
app = FastAPI()
```


