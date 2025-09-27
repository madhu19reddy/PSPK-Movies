# PSPK-Movies



-- Persons (actors, directors, crew)
CREATE TABLE persons (
  person_id INT AUTO_INCREMENT PRIMARY KEY,
  full_name VARCHAR(200) NOT NULL,
  birth_date DATE,
  bio TEXT
);

-- Movies
CREATE TABLE movies (
  movie_id INT AUTO_INCREMENT PRIMARY KEY,
  title VARCHAR(255) NOT NULL,
  release_date DATE,
  runtime_minutes SMALLINT,
  language VARCHAR(50) DEFAULT 'Telugu',
  budget BIGINT,
  box_office BIGINT,
  synopsis TEXT,
  UNIQUE(title, release_date)
);

-- Genres
CREATE TABLE genres (
  genre_id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100) UNIQUE NOT NULL
);

-- Movie Directors (link movies to persons)
CREATE TABLE movie_directors (
  movie_id INT PRIMARY KEY,
  person_id INT NOT NULL,
  FOREIGN KEY (movie_id) REFERENCES movies(movie_id) ON DELETE CASCADE,
  FOREIGN KEY (person_id) REFERENCES persons(person_id) ON DELETE CASCADE
);

-- Roles (cast members)
CREATE TABLE roles (
  role_id INT AUTO_INCREMENT PRIMARY KEY,
  movie_id INT NOT NULL,
  person_id INT NOT NULL,
  character_name VARCHAR(255),
  billing_order SMALLINT,
  role_type ENUM('Actor','Cameo','Child','Voice','Other') DEFAULT 'Actor',
  FOREIGN KEY (movie_id) REFERENCES movies(movie_id) ON DELETE CASCADE,
  FOREIGN KEY (person_id) REFERENCES persons(person_id) ON DELETE CASCADE
);

-- Movie ↔ Genres (many-to-many)
CREATE TABLE movie_genres (
  movie_id INT NOT NULL,
  genre_id INT NOT NULL,
  PRIMARY KEY (movie_id, genre_id),
  FOREIGN KEY (movie_id) REFERENCES movies(movie_id) ON DELETE CASCADE,
  FOREIGN KEY (genre_id) REFERENCES genres(genre_id) ON DELETE CASCADE
);

-- ================================================
-- DATA INSERTION
-- ================================================

-- Persons
INSERT INTO persons (full_name, birth_date) VALUES
('Pawan Kalyan', '1971-09-02'),
('Trivikram Srinivas', '1971-10-07'),
('Harish Shankar', '1979-03-31'),
('K. Raghavendra Rao', '1942-05-23'),
('S. Thaman', '1983-11-16'),
('Shruti Haasan', '1986-01-28'),
('Kajal Aggarwal', '1985-06-19');

SELECT * FROM persons;

-- Genres
INSERT INTO genres (name) VALUES
('Action'), ('Drama'), ('Romance'), ('Comedy'), ('Masala'), ('Sports');

SELECT * FROM genres;

-- Movies
INSERT INTO movies (title, release_date, runtime_minutes, language, budget, box_office, synopsis)
VALUES
('Gabbar Singh', '2012-05-11', 158, 'Telugu', 300000000, 400000000, 'Action film starring Pawan Kalyan as a fearless cop.'),
('Attarintiki Daredi', '2013-08-15', 170, 'Telugu', 400000000, 800000000, 'Family drama with comedy and emotion.'),
('Thammudu', '1999-04-16', 146, 'Telugu', 50000000, 150000000, 'Sports drama that boosted his popularity.');

SELECT * FROM movies;

-- Movie Directors
INSERT INTO movie_directors (movie_id, person_id) VALUES
((SELECT movie_id FROM movies WHERE title='Gabbar Singh'), (SELECT person_id FROM persons WHERE full_name='Harish Shankar')),
((SELECT movie_id FROM movies WHERE title='Attarintiki Daredi'), (SELECT person_id FROM persons WHERE full_name='Trivikram Srinivas')),
((SELECT movie_id FROM movies WHERE title='Thammudu'), (SELECT person_id FROM persons WHERE full_name='K. Raghavendra Rao'));

SELECT * FROM movie_directors;

-- Movie Genres
INSERT INTO movie_genres (movie_id, genre_id)
VALUES
((SELECT movie_id FROM movies WHERE title='Gabbar Singh'), (SELECT genre_id FROM genres WHERE name='Action')),
((SELECT movie_id FROM movies WHERE title='Gabbar Singh'), (SELECT genre_id FROM genres WHERE name='Masala')),
((SELECT movie_id FROM movies WHERE title='Attarintiki Daredi'), (SELECT genre_id FROM genres WHERE name='Drama')),
((SELECT movie_id FROM movies WHERE title='Thammudu'), (SELECT genre_id FROM genres WHERE name='Sports'));

SELECT * FROM movie_genres;

-- Roles
INSERT INTO roles (movie_id, person_id, character_name, billing_order)
VALUES
((SELECT movie_id FROM movies WHERE title='Gabbar Singh'), (SELECT person_id FROM persons WHERE full_name='Pawan Kalyan'), 'Gabbar Singh', 1),
((SELECT movie_id FROM movies WHERE title='Attarintiki Daredi'), (SELECT person_id FROM persons WHERE full_name='Pawan Kalyan'), 'Gautham', 1),
((SELECT movie_id FROM movies WHERE title='Thammudu'), (SELECT person_id FROM persons WHERE full_name='Pawan Kalyan'), 'Subrahmanyam', 1);

SELECT * FROM roles;
