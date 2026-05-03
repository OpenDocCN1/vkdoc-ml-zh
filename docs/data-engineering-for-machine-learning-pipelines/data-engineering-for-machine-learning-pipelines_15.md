# GET 请求，列出所有学生
@dbapp.get("/AllStudents")
def get_students() -> dict:
cursor = database_creds.cursor()
cursor.execute("Select * from students")
result = cursor.fetchall()
return { "students" : result }

