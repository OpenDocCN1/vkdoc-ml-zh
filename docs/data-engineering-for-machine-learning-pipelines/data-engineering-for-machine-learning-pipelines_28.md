# create a S3 client
s3_client = session.client('s3')
file_to_upload = '/Users/pk/Documents/abcd.csv'
bucket_name = 'bpro-dev-bucket-1'
try:
s3_client.upload_file(
file_to_upload,
bucket_name,
'uploaded_abcd.csv'
)
print(f"The file {file_to_upload} has been uploaded successfully \
in {bucket_name} S3 bucket")
except Exception as e:
print(f"Exception {e} has occurred while uploading \
{file_to_upload}")
```

在上述示例中，我们将 AWS 凭证存储在一个配置文件中，并从该文件中调用所需的访问密钥和访问密钥。一种良好的安全实践是创建一个隐藏文件夹来存储凭证。隐藏文件夹可以提供一定程度的安全性，但请记住，任何拥有开发环境 root 访问权限的人都可以看到这些凭证。
当你使用像 git 这样的版本控制系统时，默认情况下，将文件添加到仓库时不会包含隐藏文件夹。在你的 git 仓库中拥有一个 `.gitignore` 文件也非常重要，该文件用于指定要忽略哪些文件。

以下是一个非常基础的 `.gitignore` 文件：

```

