# flask数据库迁移

## 1.模型向数据库迁移

```python
python manage.py db init
python manage.py db migrate -m ""
python manage.py upgrade
```

## 2.反向生成

```python
pip install flask-sqlacodegen
flask-sqlacodegen mysql+pymysql://user:password@ip/database --outfile filename --flask
#################################
#反向生成时一定要接加mysql+pymysql#
#################################

```

