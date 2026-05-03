# @@protoc_insertion_point(module_scope)
```

现在，让我们看看我们的生产者脚本：

```
from confluent_kafka import Producer
import model_pb2
from config import config
class student:
def __init__(self, student_id, student_name, course):
self.student_id = student_id
self.student_name = student_name
self.course = course
def callback(error, msg):
if error:
print(f"消息投递失败，原因：{error}")
else:
print(f"{msg.topic()} 的消息已投递至分区 {msg.partition()}")
producer = Producer(config)
student_data = model_pb2.student_data()
data = [
student(55052,"Johndoe","Chem101" ),
student(55053,"TestStudent","Physics"),
student(55054,"JaneDoe","Biology")
]
for i in data:
student_record = student_data.student_records.add()
student_record.student_id = i.student_id
student_record.student_name = i.student_name
student_record.course = i.course
serialized_data = student_data.SerializeToString()

