# Practice session bonus point for Data Engineering

1. Copy the repo and run ```docker compose up -d```
2. Go to duckdb bash using ```docker exec -it duckdb bash```
3. Run bash command ```curl "https://randomuser.me/api/?results=100&format=csv" -o random_users.csv```
4. Start duckdb using ```duckdb```
5. Run the following SQL command ```COPY (
    SELECT 
        gender,
        "name.title" AS name_title,
        'First' || row_number() OVER () AS name_first,
        'Last' || row_number() OVER () AS name_last,
        'Street' AS location_street_name,
        "location.city" AS location_city,
        "location.state" AS location_state,
        "location.country" AS location_country,
        '****' AS location_postcode,
        '****' AS location_coordinates_latitude,
        '****' AS location_coordinates_longitude,
        "location.timezone.offset" AS location_timezone_offset,
        'Masked Location' AS location_timezone_description,
        'masked@example.com' AS email,
        'UUID' || row_number() OVER () AS login_uuid,
        'User' || row_number() OVER () AS login_username,
        '******' AS login_password,
        '******' AS login_salt,
        '****' AS login_md5,
        '****' AS login_sha1,
        '****' AS login_sha256,
        "dob.date" AS dob_date,
        "dob.age" AS dob_age,
        "registered.date" AS registered_date,
        "registered.age" AS registered_age,
        substr(phone, 1, 3) || '****' || substr(phone, -4) AS phone,
        substr(cell, 1, 3) || '****' || substr(cell, -4) AS cell,
        "id.name" AS id_name,
        'ID****' AS id_value,
        '****' AS picture_large,
        '****' AS picture_medium,
        '****' AS picture_thumbnail,
        nat
    FROM read_csv_auto('./random_users.csv')
) 
TO './anonymized_users.csv' (HEADER, DELIMITER ',');```
6. You can view if it saved correctly by using ```SELECT * FROM read_csv_auto('./anonymized_users.csv') LIMIT 5;```
7. Now use this anonymized_users.csv and save a table to duckdb ```CREATE TABLE anonymized_tbl AS
SELECT * FROM read_csv_auto('./anonymized_users.csv');```
8. To get encryption key run ```PRAGMA add_parquet_key('key256', '${ENCRYPTION_KEY}');```
9. Copy table to encrypted parquet file ```COPY anonymized_tbl TO './anonymized_users_encrypted.parquet'
  (FORMAT 'parquet', ENCRYPTION_CONFIG {footer_key: 'key256'});```
10. Read the encrypted parquet file using ```SELECT * FROM read_parquet('./anonymized_users_encrypted.parquet', encryption_config = {footer_key: 'key256'});```
11. Remove the table we saved into duckdb ```DROP TABLE IF EXISTS anonymized_tbl;```
12. Remove the unencrypted anonymized csv by running in bash ```rm anonymized_users.csv```
13. Finally remove the original csv by running in bash ```rm random_users.csv```
