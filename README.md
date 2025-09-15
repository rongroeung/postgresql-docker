# Setup Database with PostgreSQL in Docker

## I. Setup PostgreSQL in Docker

### 1. Pull the PostgreSQL Image
#### >>> Use the following command to pull the official PostgreSQL image:
```
docker pull postgres
```

### 2. Run a PostgreSQL Container
#### >>> Create local path where we want to store the PostgreSQL data:
```
mkdir /opt/postgres-data
```
#### >>> You can run a PostgreSQL container with the following command:
```
docker run --name postgres-db -p 5432:5432 --restart always \
-e POSTGRES_PASSWORD=Passw0rd \
-v /opt/postgres-data:/var/lib/postgresql/data \
-d postgres
```
#### >>> --name postgres-db: Assigns a name to the container ("postgres-db" in this case).
#### >>> --restart always: Set restart policy of container to always
#### >>> -e POSTGRES_PASSWORD=Passw0rd: Sets the password for the PostgreSQL superuser "postgres" to "Passw0rd".
#### >>> -v /opt/postgres-data:/var/lib/postgresql/data: This is the key part for mounting the volume.
#### >>> -d: Runs the container in the background.

#### >>> This command connects you to the PostgreSQL server running in the container as the user "postgres".

## II. Setup Database

### 1. Access the PostgreSQL Container
#### >>> To access the PostgreSQL container using psql, run the following command:
```
docker exec -it postgres-db psql -U postgres
```
#### >>> This command connects you to the PostgreSQL server running in the container as the user "postgres".

### 2. Switch to the `postgres` Database
#### >>> Once you are inside the psql interactive shell, switch to the default postgres database:
```
\c postgres
```

### 3. Create a New Database
#### >>> Now, you can create a new database:
```
CREATE DATABASE crwebdb;
```

### 4. Verify Database Creation
#### >>> You can verify that the database has been created by listing all the databases in the PostgreSQL server using the \l command:
```
\l
```

### 5. Create a New User
#### >>> Use the following SQL command to create a new user named 'crwebdb':
```
CREATE USER crwebdb WITH PASSWORD 'Passw0rd';
```

### 6. Grant Privileges
#### >>> Grant all privileges on the 'crwebdb' database to the 'crwebdb' user:
```
GRANT ALL PRIVILEGES ON DATABASE crwebdb TO crwebdb;
```

### 7. Grant All Privileges
#### >>> Use the ALTER ROLE command to grant all privileges to the crwebdb user and make it a superuser:
```
ALTER ROLE crwebdb SUPERUSER;
```
#### >>> This command will grant superuser privileges to the crwebdb user, providing them with all permissions in the PostgreSQL database.

### 8. Verify User Creation
#### >>> You can verify that the user has been created and granted all privileges by the following command:
```
\du
```

### 9. Exit PostgreSQL Prompt
#### >>> Once you have created the user and granted privileges, exit the PostgreSQL prompt:
```
\q
```

## III. Create Tables Functions and Insert Data

### 1. Access the PostgreSQL with pgAdmin
#### >>> Access `crwebdb` database using pgAdmin

### 2. Create Tables
#### >>> Run this script below to create 4 tables:
```
-- Create table tbl_content
CREATE TABLE tbl_content (
    id VARCHAR(31) PRIMARY KEY NOT NULL,
    title VARCHAR(255),
    kh_title VARCHAR(511),
    sub_title VARCHAR(255),
    kh_sub_title VARCHAR(511),
    create_time VARCHAR(255),
    blocked BOOLEAN
);

-- Create table tbl_description
CREATE TABLE tbl_description (
    id VARCHAR(31) PRIMARY KEY NOT NULL,
    text VARCHAR(4095),
    kh_text VARCHAR(8191),
    blocked BOOLEAN,
    content_id VARCHAR(31),
    FOREIGN KEY (content_id) REFERENCES tbl_content(id)
);

-- Create table tbl_media
CREATE TABLE tbl_media (
    id VARCHAR(31) PRIMARY KEY NOT NULL,
    url VARCHAR(255),
    name VARCHAR(255),
    note VARCHAR(2047),
    blocked BOOLEAN,
    content_id VARCHAR(31),
    FOREIGN KEY (content_id) REFERENCES tbl_content(id)
);

-- Create table tbl_youtube
CREATE TABLE tbl_youtube (
    id VARCHAR(31) PRIMARY KEY NOT NULL,
    title VARCHAR(255),
    kh_title VARCHAR(511),
    video_url VARCHAR(255),
    duration VARCHAR(255),
    publish_date VARCHAR(255),
    thumbnail_url VARCHAR(255),
    thumbnail_name VARCHAR(255),
    blocked BOOLEAN,
    content_id VARCHAR(31),
    FOREIGN KEY (content_id) REFERENCES tbl_content(id)
);
```

### 3. Create Insert Functions
#### >>> Run this script below to create 4 functions to insert data into the 4 tables:
```
-- Create a function for inserting a row into tbl_content
CREATE OR REPLACE FUNCTION insert_into_tbl_content(
    p_id VARCHAR(31),
    p_title VARCHAR(255),
    p_kh_title VARCHAR(511),
    p_sub_title VARCHAR(255),
    p_kh_sub_title VARCHAR(511),
    p_create_time VARCHAR(255),
    p_blocked BOOLEAN
)
RETURNS VOID AS $$
BEGIN
    INSERT INTO public.tbl_content(id, title, kh_title, sub_title, kh_sub_title, create_time, blocked)
    VALUES (p_id, p_title, p_kh_title, p_sub_title, p_kh_sub_title, p_create_time, p_blocked);
END;
$$ LANGUAGE plpgsql;

-- Create a function for inserting a row into tbl_description
CREATE OR REPLACE FUNCTION insert_into_tbl_description(
    p_id VARCHAR(31),
    p_text VARCHAR(4095),
    p_kh_text VARCHAR(8191),
    p_blocked BOOLEAN,
    p_content_id VARCHAR(31)
)
RETURNS VOID AS $$
BEGIN
    INSERT INTO public.tbl_description(id, text, kh_text, blocked, content_id)
    VALUES (p_id, p_text, p_kh_text, p_blocked, p_content_id);
END;
$$ LANGUAGE plpgsql;

-- Create a function for inserting a row into tbl_media
CREATE OR REPLACE FUNCTION insert_into_tbl_media(
    p_id VARCHAR(31),
    p_url VARCHAR(255),
    p_name VARCHAR(255),
    p_note VARCHAR(2047),
    p_blocked BOOLEAN,
    p_content_id VARCHAR(31)
)
RETURNS VOID AS $$
BEGIN
    INSERT INTO public.tbl_media(id, url, name, note, blocked, content_id)
    VALUES (p_id, p_url, p_name, p_note, p_blocked, p_content_id);
END;
$$ LANGUAGE plpgsql;

-- Create a function for inserting a row into tbl_youtube
CREATE OR REPLACE FUNCTION insert_into_tbl_youtube(
    p_id VARCHAR(31),
    p_title VARCHAR(255),
    p_kh_title VARCHAR(511),
    p_video_url VARCHAR(255),
    p_duration VARCHAR(255),
    p_publish_date VARCHAR(255),
    p_thumbnail_url VARCHAR(255),
    p_thumbnail_name VARCHAR(255),
    p_blocked BOOLEAN,
    p_content_id VARCHAR(31)
)
RETURNS VOID AS $$
BEGIN
    INSERT INTO public.tbl_youtube(id, title, kh_title, video_url, duration, publish_date, thumbnail_url, thumbnail_name, blocked, content_id)
    VALUES (p_id, p_title, p_kh_title, p_video_url, p_duration, p_publish_date, p_thumbnail_url, p_thumbnail_name, p_blocked, p_content_id);
END;
$$ LANGUAGE plpgsql;
```
#### >>> This is sample script to call functions to insert data:
```
-- Call the function to insert a row into tbl_content
SELECT insert_into_tbl_content('your_id_value', 'your_title_value', 'your_kh_title_value', 'your_sub_title_value', 'your_kh_sub_title_value', 'your_create_time_value', false);
-- Call the function to insert a row into tbl_description
SELECT insert_into_tbl_description('your_id_value', 'your_text_value', 'your_kh_text_value', false, 'your_content_id_value');
-- Call the function to insert a row into tbl_media
SELECT insert_into_tbl_media('your_id_value', 'your_url_value', 'your_name_value', 'your_note_value', false, 'your_content_id_value');
-- Call the function to insert a row into tbl_youtube
SELECT insert_into_tbl_youtube('your_id_value', 'your_title_value', 'your_kh_title_value', 'your_video_url_value', 'your_duration_value', 'your_publish_date_value', 'your_thumbnail_url_value', 'your_thumbnail_name_value', false, 'your_content_id_value');
```

### 4. Insert Data
#### >>> Restore Backup Data in `pgAdmin` by using file `crwebdb_20241029`
#### >>> Open `pgAdmin` > `crwebdb` > `Schemas` > `Tables` > Right click on each table > `Restore...` > Choose Filename `crwebdb_20241029` > `Restore`
