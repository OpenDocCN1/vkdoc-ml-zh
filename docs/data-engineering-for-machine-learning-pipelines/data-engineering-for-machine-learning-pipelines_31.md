# reading the configuration file
config.read('aws_rds_credentials.cfg')
dbname = config.get("rds", "database_name")
user = config.get("rds", "username")
password = config.get("rds", "password" )
host = config.get("rds", "endpoint" )
port = config.get("rds", "port" )

