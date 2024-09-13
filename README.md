
# **Movie and TV Show Database - README**
## **Overview**
The Movie and TV Show Database is designed to store, manage, and query information related to films, TV shows, actors, genres, and distribution mediums. It utilizes a relational database structure in MySQL with supertypes, subtypes, foreign keys, and join tables to maintain data integrity and allow efficient querying.

This document explains the database schema, key concepts (including primary keys, foreign keys, and relationships), and provides examples of how to interact with the database, along with several common use cases.

---

## **Database Entities and Attributes**

### **1. Media (Supertype Table)**
The *Media* table serves as the supertype for movies and TV shows, containing attributes common to both. Each media item has a unique identifier and attributes like title, genre, director, and more. It now also includes **Titles** for storing alternate titles and **Languages** for storing multiple languages associated with the media.

#### **Columns**:
| Column           | Data Type                           | Description                                                   |
|------------------|-------------------------------------|---------------------------------------------------------------|
| **MediaID**      | `INT` (Primary Key, Auto-Increment) | Unique identifier for each media item (either movie or TV show).|
| **Titles**       | `TEXT`                              | List of alternate titles (e.g., international or working titles).|
| **GenreName**    | `VARCHAR(255)`                      | Foreign key referencing the `Genre` table (`GenreName`).        |
| **ReleaseDate**  | `DATE`                              | The release date of the media item.                            |
| **Director**     | `VARCHAR(255)`                      | The director of the media item.                                |
| **Duration**     | `INT`                               | Duration of the media in minutes.                              |
| **MediaType**    | `ENUM('Movie', 'TVShow')`           | Indicates whether the media is a movie or a TV show.           |
| **Rating**       | `VARCHAR(10)`                       | Audience rating (e.g., PG-13, R, TV-MA).                       |
| **Languages**    | `TEXT`                              | List of languages (original, dubbed, subtitled).               |
| **CountryOfOrigin** | `VARCHAR(50)`                    | The country where the media was produced.                      |
| **Budget**       | `DECIMAL(15,2)`                     | Production cost of the media.                                  |
| **Awards**       | `TEXT`                              | List or description of awards won by the media.                |
| **Synopsis**     | `TEXT`                              | A brief plot summary.                                          |
| **AudienceScore**| `DECIMAL(3,1)`                      | Aggregate score from audience reviews (out of 10).             |
| **CriticScore**  | `DECIMAL(5,2)`                      | Aggregate score from critic reviews (out of 100).              |
| **IsFranchise**  | `BOOLEAN`                           | Indicates if the media is part of a franchise.                 |

### **2. Movie (Subtype Table)**
The *Movie* table inherits from *Media* and includes additional attributes specific to movies.

#### **Columns**:
| Column          | Data Type                           | Description                                                   |
|-----------------|-------------------------------------|---------------------------------------------------------------|
| **MovieID**     | `INT` (Primary Key, Auto-Increment) | Unique identifier for each movie.                             |
| **MediaID**     | `INT`                               | Foreign key referencing `Media(MediaID)`.                      |
| **BoxOfficeGross** | `DECIMAL(15,2)`                  | Total box office revenue of the movie.                        |
| **SequelOf**    | `INT`                               | Foreign key referencing `Movie(MovieID)` if the movie is a sequel.|

### **3. TV Show (Subtype Table)**
The *TV Show* table inherits from *Media* and includes additional attributes specific to TV shows.

#### **Columns**:
| Column                 | Data Type                           | Description                                                   |
|------------------------|-------------------------------------|---------------------------------------------------------------|
| **TVShowID**           | `INT` (Primary Key, Auto-Increment) | Unique identifier for each TV show.                           |
| **MediaID**            | `INT`                               | Foreign key referencing `Media(MediaID)`.                      |
| **TotalEpisodes**      | `INT`                               | Total number of episodes across all seasons.                  |
| **IsOngoing**          | `BOOLEAN`                           | Indicates if the show is still airing.                        |
| **FinalEpisodeDate**   | `DATE`                              | Date of the last episode if the show has ended.               |
| **AverageEpisodeDuration** | `INT`                          | Average runtime per episode in minutes.                       |

### **4. Actor**
The *Actor* table stores information about individual actors, including their names, nationality, and awards won.

#### **Columns**:
| Column         | Data Type                           | Description                                                   |
|----------------|-------------------------------------|---------------------------------------------------------------|
| **ActorID**    | `INT` (Primary Key, Auto-Increment) | Unique identifier for each actor.                             |
| **FirstName**  | `VARCHAR(255)`                      | First name of the actor.                                      |
| **LastName**   | `VARCHAR(255)`                      | Last name of the actor.                                       |
| **DateOfBirth**| `DATE`                              | Actor's date of birth.                                        |
| **Nationality**| `VARCHAR(50)`                       | Actor's nationality.                                          |
| **Gender**     | `VARCHAR(10)`                       | Gender of the actor.                                          |
| **AwardsCount**| `INT`                               | Total number of awards won by the actor.                      |

### **5. Genre**
The *Genre* table categorizes media items into genres (e.g., Drama, Sci-Fi), and includes options for hierarchical relationships between genres.

#### **Columns**:
| Column           | Data Type                           | Description                                                   |
|------------------|-------------------------------------|---------------------------------------------------------------|
| **GenreName**    | `VARCHAR(255)` (Primary Key)        | Unique name of the genre.                                     |
| **Description**  | `TEXT`                              | Description of the genre.                                     |
| **ParentGenre**  | `VARCHAR(255)`                      | Foreign key referencing `Genre(GenreName)`, if sub-genre.     |
| **PopularityRank** | `INT`                             | Popularity ranking of the genre.                              |

### **6. Medium**
The *Medium* table stores information about different distribution methods for media, such as Blu-Ray, Streaming, or DVD.

#### **Columns**:
| Column         | Data Type                           | Description                                                   |
|----------------|-------------------------------------|---------------------------------------------------------------|
| **MediumID**   | `INT` (Primary Key, Auto-Increment) | Unique identifier for each medium.                            |
| **MediaType**  | `VARCHAR(255)`                      | The type of medium (e.g., Blu-Ray, Streaming).                |
| **Resolution** | `VARCHAR(50)`                       | Video resolution (e.g., 1080p, 4K).                           |
| **Price**      | `DECIMAL(10,2)`                     | Price of purchasing or renting the medium.                    |
| **ReleaseDate**| `DATE`                              | The release date of the medium.                               |
| **StorageSize**| `VARCHAR(50)`                       | File size or storage capacity (e.g., 50GB).                   |

---

## **Relationships and Join Tables**

### **1. Media to Movie / TV Show (1:1 Relationship)**
Each record in the *Media* table is either a movie or a TV show, identified by the `MediaType` attribute. The *Movie* and *TV Show* tables have a 1:1 relationship with *Media* via the `MediaID` foreign key.

### **2. Media to Genre (Many-to-One Relationship)**
Each *Media* record belongs to one genre, linked by the `GenreName` foreign key in the *Media* table. A genre can have multiple media items.

### **3. Actor to Media (Many-to-Many Relationship)**
A single actor can appear in multiple movies or TV shows, and a single movie or TV show can feature multiple actors. This is modeled with the **MediaActor** join table.

#### **MediaActor Table (Join Table)**:
| Column       | Data Type                           | Description                                                   |
|--------------|-------------------------------------|---------------------------------------------------------------|
| **MediaID**  | `INT`                               | Foreign key referencing `Media(MediaID)`.                      |
| **ActorID**  | `INT`                               | Foreign key referencing `Actor(ActorID)`.                      |
| **Role**     | `VARCHAR(255)`                      | Name of the character portrayed by the actor.                 |
| **IsLeadRole** | `BOOLEAN`                         | Indicates if the actor played a lead role (TRUE/FALSE).        |

*Composite Primary Key:* `(MediaID, ActorID)`

### **4. Media to Medium (Many-to-Many Relationship)**
A media item can be available in multiple formats (e.g., Blu-Ray, Streaming), and a single medium can have multiple movies or TV shows. This is modeled with the **MediaMedium** join table.

#### **MediaMedium Table (Join Table)**:
| Column       | Data Type                           | Description                                                   |
|--------------|-------------------------------------|---------------------------------------------------------------|
| **MediaID**  | `INT`                               | Foreign key referencing `Media(MediaID)`.                      |
| **MediumID** | `INT`                               | Foreign key referencing `Medium(MediumID)`.                    |

*Composite Primary Key:* `(MediaID, MediumID)`

---

### **Use Cases**

Find All Movies Directed by a Specific Director:
- **Description**: A user wants to find all the movies directed by **Christopher Nolan** and get their release dates, box office gross, and audience scores.

Get High-Scoring Sci-Fi Movies Released After 2010:
- **Description**: A user wants to retrieve a list of **Sci-Fi** movies with **critic scores** higher than 85 and that were released after the year 2010.

Find All Actors Who Played Lead Roles in Specific Genres:
- **Description**: A user wants to find all actors who played **lead roles** in movies categorized under the **Drama** genre.

Find All Media Available in 4K Resolution on Blu-Ray:
- **Description**: A user wants to find all **movies or TV shows** that are available on **4K Blu-Ray**, displaying their titles and mediums.

Retrieve All Movies That Are Part of a Franchise:
- **Description**: A user wants to find all **movies** that are part of a **franchise**, showing the titles and whether they have sequels.

Find Movies with Alternate Titles Released in Multiple Languages:
- **Description**: A user wants to retrieve all **movies** that have **alternate titles** and are available in multiple **languages**, showing the titles and languages.

---
