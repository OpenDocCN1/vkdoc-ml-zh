# GET 请求，根据学生 ID 获取姓名
@dbapp.get("/student/{id}")
def get_student(id: int) -> dict:
cursor = database_creds.cursor()
cursor.execute(f"Select name from student where id = {id}")
result = cursor.fetchone()
return { "student" : result }

