# GET 请求，根据学生姓名获取课程
@dbapp.get("/stucourse/{name}")
def stucourse(name: str) -> dict:
cursor = database_creds.cursor()
cursor.execute(f"Select course from student where name = {name}")
result = cursor.fetchall()
return { "student" : result }

