# Store-the-content-of-JSON-file-into-a-database
# JSON file onto the Database

# Create the database from Python and Extract the Name, Title and Role content from the JSON file input by user

import jsonimport urllib.request
import sslimport sqlite3
conn = sqlite3.connect('roasterdb.sqlite')
cur = conn.cursor()

cur.executescript ( '''
DROP TABLE IF EXISTS User;
DROP TABLE IF EXISTS Member;
DROP TABLE IF EXISTS Course;

CREATE TABLE User (    
  id   INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT UNIQUE,   
  name TEXT UNIQUE);

CREATE TABLE Course(    
  id INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT UNIQUE,    
  title TEXT UNIQUE);

CREATE TABLE Member (    
  user_id INTEGER,    
  course_id INTEGER,    
  role INTEGER, 
  PRIMARY KEY(user_id,course_id)
  );
  ''')

fname = input('Enter file name')
str_data = open(fname).read()
json_data = json.loads(str_data)
#print(str_data)

for entry in json_data:    
  name = entry[0]    
  title = entry[1]    
  role = entry[2]    
  print((name,title,role))
  
  cur.execute('''INSERT OR IGNORE INTO User(name) VALUES (?) ''',(name, ) )    
  cur.execute('''SELECT id FROM User    WHERE name = ?''', (name, ) )    
  
  user_id = cur.fetchone()[0]
  
  cur.execute('''INSERT OR IGNORE INTO Course(title)  VALUES (?) ''' , (title, ) )    
  cur.execute('''SELECT id FROM Course WHERE title = ? ''', (title, )  )   
  course_id = cur.fetchone()[0]
  cur.execute(''' INSERT OR REPLACE INTO Member(user_id,course_id,role) VALUES (?,?,?)''', (user_id,course_id,role))
  
  conn.commit()

cur.close()
