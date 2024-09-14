# Setup Database with PostgreSQL in Docker

## I. Setup PostgreSQL in Docker

### 1. Pull the PostgreSQL Image
#### >>> Use the following command to pull the official PostgreSQL image:
```
sudo docker pull postgres
```

### 2. Run a PostgreSQL Container
#### >>> You can run a PostgreSQL container with the following command:
```
sudo docker run --name postgres-db -p 5432:5432 --restart always -e POSTGRES_PASSWORD=Passw0rd -d postgres
```
#### >>> --name postgres-db: Assigns a name to the container ("postgres-db" in this case).
#### >>> --restart always: Set restart policy of container to always
#### >>> -e POSTGRES_PASSWORD=Passw0rd: Sets the password for the PostgreSQL superuser "postgres" to "Passw0rd".
#### >>> -d: Runs the container in the background.

#### >>> This command connects you to the PostgreSQL server running in the container as the user "postgres".

## II. Setup Database

### 1. Access the PostgreSQL Container
#### >>> To access the PostgreSQL container using psql, run the following command:
```
sudo docker exec -it postgres-db psql -U postgres
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
    id VARCHAR(31) PRIMARY KEY,
    title VARCHAR(255),
    kh_title VARCHAR(511),
    sub_title VARCHAR(255),
    kh_sub_title VARCHAR(511)
);

-- Create table tbl_description
CREATE TABLE tbl_description (
    id VARCHAR(31) PRIMARY KEY,
    text VARCHAR(4159),
    kh_text VARCHAR(8319),
    content_id VARCHAR(31),
    FOREIGN KEY (content_id) REFERENCES tbl_content(id)
);

-- Create table tbl_media
CREATE TABLE tbl_media (
    id VARCHAR(31) PRIMARY KEY,
    url VARCHAR(255),
    name VARCHAR(255),
    content_id VARCHAR(31),
    FOREIGN KEY (content_id) REFERENCES tbl_content(id)
);

-- Create table tbl_youtube
CREATE TABLE tbl_youtube (
    id VARCHAR(31) PRIMARY KEY,
    title VARCHAR(255),
    kh_title VARCHAR(511),
    video_url VARCHAR(255),
    duration VARCHAR(255),
    publish_date VARCHAR(255),
    thumbnail_url VARCHAR(255),
    thumbnail_name VARCHAR(255),
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
    p_kh_sub_title VARCHAR(511)
)
RETURNS VOID AS $$
BEGIN
    INSERT INTO public.tbl_content(id, title, kh_title, sub_title, kh_sub_title)
    VALUES (p_id, p_title, p_kh_title, p_sub_title, p_kh_sub_title);
END;
$$ LANGUAGE plpgsql;

-- Create a function for inserting a row into tbl_description
CREATE OR REPLACE FUNCTION insert_into_tbl_description(
    p_id VARCHAR(31),
    p_text VARCHAR(4159),
    p_kh_text VARCHAR(8319),
    p_content_id VARCHAR(31)
)
RETURNS VOID AS $$
BEGIN
    INSERT INTO public.tbl_description(id, text, kh_text, content_id)
    VALUES (p_id, p_text, p_kh_text, p_content_id);
END;
$$ LANGUAGE plpgsql;

-- Create a function for inserting a row into tbl_media
CREATE OR REPLACE FUNCTION insert_into_tbl_media(
    p_id VARCHAR(31),
    p_url VARCHAR(255),
    p_name VARCHAR(255),
    p_content_id VARCHAR(31)
)
RETURNS VOID AS $$
BEGIN
    INSERT INTO public.tbl_media(id, url, name, content_id)
    VALUES (p_id, p_url, p_name, p_content_id);
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
    p_content_id VARCHAR(31)
)
RETURNS VOID AS $$
BEGIN
    INSERT INTO public.tbl_youtube(id, title, kh_title, video_url, duration, publish_date, thumbnail_url, thumbnail_name, content_id)
    VALUES (p_id, p_title, p_kh_title, p_video_url, p_duration, p_publish_date, p_thumbnail_url, p_thumbnail_name, p_content_id);
END;
$$ LANGUAGE plpgsql;
```
#### >>> This is sample script to call functions to insert data:
```
-- Call the function to insert a row into tbl_content
SELECT insert_into_tbl_content('your_id_value', 'your_title_value', 'your_kh_title_value', 'your_sub_title_value', 'your_kh_sub_title_value');
-- Call the function to insert a row into tbl_description
SELECT insert_into_tbl_description('your_id_value', 'your_text_value', 'your_kh_text_value', 'your_content_id_value');
-- Call the function to insert a row into tbl_media
SELECT insert_into_tbl_media('your_id_value', 'your_url_value', 'your_name_value', 'your_content_id_value');
-- Call the function to insert a row into tbl_youtube
SELECT insert_into_tbl_youtube('your_id_value', 'your_title_value', 'your_kh_title_value', 'your_video_url_value', 'your_duration_value', 'your_publish_date_value', 'your_thumbnail_url_value', 'your_thumbnail_name_value', 'your_content_id_value');
```

### 4. Insert Data
#### >>> This is the actual script to call functions to insert data:
```
-- Home Page
-- Insert data for content_id: 01001001
-- Call the function to insert a row into tbl_content
SELECT insert_into_tbl_content('01001001', 'Rebuilding Cambodia', 'កសាងប្រទេសកម្ពុជាឡើងវិញ', 'Love God. Love Others. Make Disciples', 'ស្រឡាញ់ព្រះ ស្រឡាញ់អ្នកដទៃ បង្កើតសិស្ស')
UNION ALL
-- Call the function to insert a row into tbl_media
SELECT insert_into_tbl_media('md00001', 'https://crossroadscambodia.church/cr-drive/cr-photos/About--Cover-001-(3200x2100).jpg', 'Picture Of Board', '01001001')
UNION ALL
SELECT insert_into_tbl_media('md00002', 'https://crossroadscambodia.church/cr-drive/cr-photos/About--Cover-001-(3200x2100).jpg', 'Picture Of Board', '01001001')
UNION ALL
SELECT insert_into_tbl_media('md00003', 'https://crossroadscambodia.church/cr-drive/cr-photos/About--Cover-001-(3200x2100).jpg', 'Picture Of Board', '01001001')
UNION ALL

-- Insert data for content_id: 01002001
-- Call the function to insert a row into tbl_content
SELECT insert_into_tbl_content('01002001', 'Our Discipleship Journey', 'ដំណើរបង្កើតសិស្សរបស់យើង', NULL, NULL)
UNION ALL
-- Call the function to insert a row into tbl_description
SELECT insert_into_tbl_description('dt00001', 'Devotional Disciples', 'សិស្សដែលស្ម័គ្រចិត្ត', '01002001')
UNION ALL
SELECT insert_into_tbl_description('dt00002', 'Discipleship Character', 'ចរិតសិស្ស', '01002001')
UNION ALL
SELECT insert_into_tbl_description('dt00003', 'Discipleship Skills', 'ជំនាញសិស្ស', '01002001')
UNION ALL
SELECT insert_into_tbl_description('dt00004', 'Biblical Doctrines', 'គោលលទ្ធិព្រះគម្ពីរ', '01002001')
UNION ALL

-- Insert data for content_id: 01003001
-- Call the function to insert a row into tbl_content
SELECT insert_into_tbl_content('01003001', 'Connect', 'ផ្សាភ្ជាប់', NULL, NULL)
UNION ALL
-- Call the function to insert a row into tbl_description
SELECT insert_into_tbl_description('dt00005', 'Share The Gospel With Them', 'ចែកចាយព្រះបន្ទូលជាមួយពួកគេ', '01003001')
UNION ALL
SELECT insert_into_tbl_description('dt00006', 'Identify Their Spiritual Interest', 'កំណត់ចំណាប់អារម្មណ៍ខាងព្រះរបស់ពួកគេ', '01003001')
UNION ALL
SELECT insert_into_tbl_description('dt00007', 'Clarify The Gospel And Remove Barriers', 'បញ្ជាក់ព្រះបន្ទូលហើយដកបំរាយ', '01003001')
UNION ALL
SELECT insert_into_tbl_description('dt00008', 'Bring Them To A Decision Point About Christ', 'នាំពួកគេមកចំណុចសម្រេចចិត្តអំពីព្រះគ្រីស្ទ', '01003001')
UNION ALL
SELECT insert_into_tbl_description('dt00009', 'Thoroughly Follow Up If They Indicate Belief And Repentance', 'តាមដានយ៉ាងហ្មត់ចត់ប្រសិនបើពួកគេបង្ហាញជំនឿនិងការប្រែចិត្ត', '01003001')
UNION ALL
-- Call the function to insert a row into tbl_media
SELECT insert_into_tbl_media('md00004', 'https://crossroadscambodia.church/cr-drive/cr-photos/About--Cover-001-(3200x2100).jpg', 'icon', '01003001')
UNION ALL

-- Insert data for content_id: 01003002
-- Call the function to insert a row into tbl_content
SELECT insert_into_tbl_content('01003002', 'Establish', 'បង្កើត', NULL, NULL)
UNION ALL
-- Call the function to insert a row into tbl_description
SELECT insert_into_tbl_description('dt00010', 'Helping Them Develop A Right Relationship With God', 'ជួយពួកគេកសាងទំនាក់ទំនងត្រឹមត្រូវជាមួយព្រះ', '01003002')
UNION ALL
SELECT insert_into_tbl_description('dt00011', 'Helping Them Develop A Right Relationship With Self', 'ជួយពួកគេកសាងទំនាក់ទំនងត្រឹមត្រូវជាមួយខ្លួនឯង', '01003002')
UNION ALL
SELECT insert_into_tbl_description('dt00012', 'Helping Them Develop A Right Relationship With Others', 'ជួយពួកគេកសាងទំនាក់ទំនងត្រឹមត្រូវជាមួយអ្នកដទៃ', '01003002')
UNION ALL
-- Call the function to insert a row into tbl_media
SELECT insert_into_tbl_media('md00005', 'https://crossroadscambodia.church/cr-drive/cr-photos/About--Cover-001-(3200x2100).jpg', 'icon', '01003002')
UNION ALL

-- Insert data for content_id: 01003003
-- Call the function to insert a row into tbl_content
SELECT insert_into_tbl_content('01003003', 'equip', 'បំពាក់', NULL, NULL)
UNION ALL
-- Call the function to insert a row into tbl_description
SELECT insert_into_tbl_description('dt00013', 'Training Them To Evangelize And Establish Others', 'បណ្តុះបណ្តាលពួកគេឱ្យផ្សាយព្រះបន្ទូលនិងបង្កើតអ្នកដទៃ', '01003003')
UNION ALL
SELECT insert_into_tbl_description('dt00014', 'Leading Them In Personal Ministry', 'ដឹកនាំពួកគេក្នុងព័ន្ធកិច្ចផ្ទាល់ខ្លួន', '01003003')
UNION ALL
SELECT insert_into_tbl_description('dt00015', 'Helping Them Begin To Understand And Develop Vision', 'ជួយពួកគេចាប់ផ្តើមយល់និងអភិវឌ្ឍចក្ខុវិស័យ', '01003003')
UNION ALL
SELECT insert_into_tbl_description('dt00016', 'Identifying Their Character Needs And Helping Them Mature And Christ Likeness', 'កំណត់តម្រូវការចរិតរបស់ពួកគេហើយជួយពួកគេឱ្យទុទិសនិងស្រដៀងនឹងព្រះគ្រីស្ទ', '01003003')
UNION ALL
-- Call the function to insert a row into tbl_media
SELECT insert_into_tbl_media('md00006', 'https://crossroadscambodia.church/cr-drive/cr-photos/About--Cover-001-(3200x2100).jpg', 'icon', '01003003')
UNION ALL

-- Insert data for content_id: 01003004
-- Call the function to insert a row into tbl_content
SELECT insert_into_tbl_content('01003004', 'connect', 'ផ្សាភ្ជាប់', NULL, NULL)
UNION ALL
-- Call the function to insert a row into tbl_description
SELECT insert_into_tbl_description('dt00017', 'Helping Them Begin To Discern Their Unique Role In The Church Kingdom', 'ជួយពួកគេចាប់ផ្តើមប្រាស្រ័យទាក់ទងនូវតួនាទីតែមួយគត់របស់ពួកគេក្នុងព្រះវិហារ / រាជាណាចក្រ', '01003004')
UNION ALL
SELECT insert_into_tbl_description('dt00018', 'Developing A Personalized Ministry Plan With Them', 'អភិវឌ្ឍផែនការព័ន្ធកិច្ចផ្ទាល់ខ្លួនជាមួយពួកគេ', '01003004')
UNION ALL
SELECT insert_into_tbl_description('dt00019', 'Connecting Them With People And Opportunities', 'ភ្ជាប់ពួកគេជាមួយមនុស្សនិងឱកាស', '01003004')
UNION ALL
-- Call the function to insert a row into tbl_media
SELECT insert_into_tbl_media('md00007', 'https://crossroadscambodia.church/cr-drive/cr-photos/About--Cover-001-(3200x2100).jpg', 'icon', '01003004')
UNION ALL

-- Insert data for content_id: 01004001
-- Call the function to insert a row into tbl_content
SELECT insert_into_tbl_content('01004001', 'Certificate In Ministry Leadership', 'សញ្ញាប័ត្រដឹកនាំព័ន្ធកិច្ច', 'The School of Missions', 'សាលាបេសកកម្ម')
UNION ALL
-- Call the function to insert a row into tbl_description
SELECT insert_into_tbl_description('dt00020', 'The School of Missions is a theological training program organized by CNEC, catered for lay pastors and church leaders in the mission fields. There are two courses in this program. Diploma in Christian Ministry serves as foundational training for students who are considering formal theological education in the near future. Certificate in Ministry Leadership equips students with the competencies necessary to serve as effective leaders in churches, Christian organizations, and other contexts.', 'សាលាបេសកកម្មគឺជាកម្មវិធីបណ្តុះបណ្តាលខាងទ្រឹស្ដីដែលរៀបចំឡើងដោយ CNEC ដែលរៀបចំសម្រាប់គ្រូគង្វាល និងអ្នកដឹកនាំព្រះវិហារ', '01004001')
UNION ALL
-- Call the function to insert a row into tbl_media
SELECT insert_into_tbl_media('md00008', 'https://crossroadscambodia.church/cr-drive/cr-photos/About--Cover-001-(3200x2100).jpg', 'Picture Of Board', '01004001')
UNION ALL

-- Insert data for content_id: 01004002
-- Call the function to insert a row into tbl_content
SELECT insert_into_tbl_content('01004002', 'Worship Ministry', 'ព័ន្ធកិច្ចថ្វាយបង្គំ', 'Sunday Worship', 'កម្មវិធីថ្វាយបង្គំថ្ងៃអាទិត្យ')
UNION ALL
-- Call the function to insert a row into tbl_description
SELECT insert_into_tbl_description('dt00021', 'Our worship team exists to not only lead us into Jesus'' heart through the medium of songs and musical instruments but also to recognize, coach, and release those who are anointed into His calling.', 'ក្រុមគោរពរបស់យើងមានដើម្បីមិនត្រឹមតែនាំយើងចូលទៅក្នុងបេះដូងរបស់ព្រះយេស៊ូវតាមរយៈឧបករណ៍ភ្លេងនិងឧបករណ៍ភ្លេងប៉ុណ្ណោះទេប៉ុន្តែថែមទាំងដើម្បីទទួលស្គាល់គ្រូបង្វឹក និងដោះលែងអ្នកដែលត្រូវបានចាក់ប្រេងក្នុងការហៅរបស់ទ្រង់។', '01004002')
UNION ALL
-- Call the function to insert a row into tbl_media
SELECT insert_into_tbl_media('md00009', 'https://crossroadscambodia.church/cr-drive/cr-photos/About--Cover-001-(3200x2100).jpg', 'Picture Of Board', '01004002')
UNION ALL

-- Insert data for content_id: 01005001
-- Call the function to insert a row into tbl_content
SELECT insert_into_tbl_content('01005001', 'Our Sunday Sermons', 'ព្រះបន្ទូលថ្ងៃអាទិត្យ', NULL, NULL)
UNION ALL
-- Call the function to insert a row into tbl_youtube
SELECT insert_into_tbl_youtube('yt00001', 'Deacon Bryan Testimony', 'ទីបន្ទាល់របស់លោកប្រាយអេន', 'https://www.youtube.com/watch?v=7sDG_CUmTQM', '36:09', '17 June 2024', 'https://crossroadscambodia.church/cr-drive/cr-photos/About--Cover-001-(3200x2100).jpg', 'Picture Of Board', '01005001')
UNION ALL
-- Call the function to insert a row into tbl_youtube
SELECT insert_into_tbl_youtube('yt00002', 'Lazarus Resurrection', 'ព្រះយេស៊ូវប្រោសឡាសាឲ្យរស់ឡើងវិញ', 'https://www.youtube.com/watch?v=7sDG_CUmTQM', '12:09', '17 June 2024', 'https://crossroadscambodia.church/cr-drive/cr-photos/About--Cover-001-(3200x2100).jpg', 'Picture Of Board', '01005001')
UNION ALL
-- Call the function to insert a row into tbl_youtube
SELECT insert_into_tbl_youtube('yt00003', 'Knowing Your Why', 'ការដឹងថាហេតុអ្វី', 'https://www.youtube.com/watch?v=7sDG_CUmTQM', '41:09', '17 June 2024', 'https://crossroadscambodia.church/cr-drive/cr-photos/About--Cover-001-(3200x2100).jpg', 'Picture Of Board', '01005001')
UNION ALL
-- Call the function to insert a row into tbl_youtube
SELECT insert_into_tbl_youtube('yt00004', 'Mother''s Day', 'ទិវាអ្នកម្ដាយ', 'https://www.youtube.com/watch?v=7sDG_CUmTQM', '36:09', '17 June 2024', 'https://crossroadscambodia.church/cr-drive/cr-photos/About--Cover-001-(3200x2100).jpg', 'Picture Of Board', '01005001')
UNION ALL

-- Insert data for content_id: 01006001
-- Call the function to insert a row into tbl_content
SELECT insert_into_tbl_content('01006001', 'Pastor Moeun Rathana', 'លោកគ្រូ មឿន រតនា', 'Senior Pastor', 'ប្រធានគ្រូគង្វាល')
UNION ALL
-- Call the function to insert a row into tbl_description
SELECT insert_into_tbl_description('dt00022', 'Pastor Moeun Rathana, guided by God''s calling, was commissioned to do church planting amongst the university students in 2008. Despite his IT and Economics degree, in 2013 he pursued M.Div. in Pastoral Studies in the Philippines, earning honors and a theological award. In 2017, he was installed to be the pastor of Crossroads Church.', 'គ្រូគង្វាល មឿន រតនា ដែលដឹកនាំដោយការត្រាស់ហៅរបស់ព្រះ ត្រូវបានចាត់តាំងឱ្យធ្វើព្រះវិហារនៅក្នុងចំណោមនិស្សិតសាកលវិទ្យាល័យក្នុងឆ្នាំ ២០០៨។ ទោះបីជាគាត់បានបញ្ចប់បរិញ្ញាបត្រផ្នែក IT និងសេដ្ឋកិច្ចក៏ដោយ នៅឆ្នាំ 2013 គាត់បានបន្តការសិក្សា M.Div ។ នៅ​ក្នុង​ការ​សិក្សា​គ្រូគង្វាល​ក្នុង​ប្រទេស​ហ្វីលីពីន ដោយ​ទទួល​បាន​កិត្តិយស និង​ពាន​រង្វាន់​ទ្រឹស្ដី។ នៅឆ្នាំ 2017 គាត់ត្រូវបានតែងតាំងជាគ្រូគង្វាលនៃព្រះវិហារ Crossroads ។', '01006001')
UNION ALL
-- Call the function to insert a row into tbl_media
SELECT insert_into_tbl_media('md00010', 'https://crossroadscambodia.church/cr-drive/cr-photos/About--Cover-001-(3200x2100).jpg', 'Picture Of Board', '01006001')
UNION ALL

-- Insert data for content_id: 01006002
-- Call the function to insert a row into tbl_content
SELECT insert_into_tbl_content('01006002', 'Ptr. Chem Menghun', 'លោក ចែម ម៉េងហ៊ុន', 'Assistant Pastor', 'ជំនួយការគ្រូគង្វាល')
UNION ALL
-- Call the function to insert a row into tbl_description
SELECT insert_into_tbl_description('dt00023', 'He embarked on his full-time ministry with Crossroads in 2017, subsequently attending Phnom Penh Bible School. In 2021, he achieved a degree in theology. Presently, he holds the position of Assistant Pastor at the church, overseeing several ministries. Additionally, he fulfills the role of Pastor for the male dormitory.', 'គាត់បានចាប់ផ្ដើមកិច្ចបម្រើផ្សាយពេញពេលជាមួយនឹងផ្លូវបំបែកនៅឆ្នាំ 2017 ហើយក្រោយមកបានចូលសាលាព្រះគម្ពីរភ្នំពេញ។ នៅឆ្នាំ 2021 គាត់បានទទួលសញ្ញាប័ត្រផ្នែកទេវវិទ្យា។ បច្ចុប្បន្ន​លោក​មាន​តួនាទី​ជា​ជំនួយការ​គ្រូគង្វាល​នៅ​ព្រះវិហារ ដោយ​មើល​ការ​ខុសត្រូវ​ក្រសួង​មួយ​ចំនួន។ លើសពីនេះទៀត គាត់បំពេញតួនាទីជាគ្រូគង្វាលសម្រាប់អន្តេវាសិកដ្ឋានបុរស។', '01006002')
UNION ALL
-- Call the function to insert a row into tbl_media
SELECT insert_into_tbl_media('md00011', 'https://crossroadscambodia.church/cr-drive/cr-photos/About--Cover-001-(3200x2100).jpg', 'Picture Of Board', '01006002')
UNION ALL

-- Insert data for content_id: 01006003
-- Call the function to insert a row into tbl_content
SELECT insert_into_tbl_content('01006003', 'Sis. Bin Dany', 'បងស្រី ប៊ីនដានី', 'Pastoral Team Member', 'សមាជិកក្រុមគ្រូគង្វាល')
UNION ALL
-- Call the function to insert a row into tbl_description
SELECT insert_into_tbl_description('dt00024', 'Sister Bin Dany became a valuable addition to the Crossroads pastoral team in 2022. She undertakes the pastoral duties for the female dormitory, provides Bible teachings in the evenings, and coordinates the worship leaders. Her multifaceted role contributes significantly to the spiritual growth and harmony of the community.', 'បងស្រី ប៊ិន ដានី បានក្លាយជាអ្នកបន្ថែមដ៏មានតម្លៃដល់ក្រុមគ្រូគង្វាលផ្លូវបំបែកក្នុងឆ្នាំ 2022។ គាត់បំពេញភារកិច្ចគ្រូគង្វាលសម្រាប់អន្តេវាសិកដ្ឋានស្ត្រី ផ្តល់ការបង្រៀនព្រះគម្ពីរនៅពេលល្ងាច និងសម្របសម្រួលអ្នកដឹកនាំថ្វាយបង្គំ។ តួនាទីពហុមុខរបស់នាងរួមចំណែកយ៉ាងសំខាន់ដល់ការលូតលាស់ខាងវិញ្ញាណ និងភាពសុខដុមរមនានៃសហគមន៍។', '01006003')
UNION ALL
-- Call the function to insert a row into tbl_media
SELECT insert_into_tbl_media('md00012', 'https://crossroadscambodia.church/cr-drive/cr-photos/About--Cover-001-(3200x2100).jpg', 'Picture Of Board', '01006003')
UNION ALL

-- Insert data for content_id: 01007001
-- Call the function to insert a row into tbl_content
SELECT insert_into_tbl_content('01007001', 'Want To Get In Touch?', 'តើលោកអ្នកចង់ភ្ជាប់ទំនាក់ទំនងជាមួយយើងទេ?', NULL, NULL);
```
