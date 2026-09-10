# Book-Recommendation-System
Book Recommendation System with collaborative filtering, cosine similarity, personalized recommendations, reading analytics, offline evaluation, and PDF reporting. Built with React, TypeScript, Vite, and Tailwind CSS.
#  Book Recommendation System

A modern, interactive **Book Recommendation & Reading Analytics Dashboard** built with React and TypeScript.

The application uses **collaborative filtering and cosine similarity** to generate personalized book recommendations, discover similar books, analyze reader behavior, evaluate recommendation performance, and visualize insights from book-rating datasets.

---

##  Features

###  Personalized Recommendations

Generate recommendations using two collaborative filtering approaches:

* **Item-Based Collaborative Filtering**

  * Finds books similar to books the user has rated positively.
  * Uses cosine similarity between book-rating vectors.
  * Provides explanations based on supporting books.

* **User-Based Collaborative Filtering**

  * Identifies users with similar reading/rating patterns.
  * Uses ratings from similar users to predict potential preferences.

* **Automatic Fallback Recommendations**

  * Handles users with no ratings using popularity-based recommendations.
  * Fills incomplete recommendation lists with highly rated community books.

###  Similar Book Discovery

Select any book and discover similar titles using:

* Cosine similarity
* Number of overlapping users
* Similarity ranking
* Genre filtering

### Analytics Dashboard

Explore detailed statistics including:

* Total books
* Total users
* Total ratings
* Average dataset rating
* Average ratings per user
* Average ratings per book
* Most-rated books
* Rating distributions
* Genre distributions
* User reading profiles
* Book-level statistics

###  Recommendation Evaluation

The system includes an offline hold-out evaluation framework with:

* Precision@5
* Recall@5
* Hit Rate@5
* Precision@10
* Recall@10
* Hit Rate@10
* Precision@20
* Recall@20
* Hit Rate@20
* Catalog Coverage
* Similarity distribution

The evaluation compares both **item-based** and **user-based** collaborative filtering.

###  Custom Dataset Upload

Upload your own:

* `books.csv`
* `ratings.csv`

The application validates and processes the uploaded datasets before rebuilding the recommendation and analytics state.

###  PDF Reporting

Generate a PDF report containing:

* Dataset statistics
* Recommendation results
* Selected user/book information
* Evaluation metrics
* Generated insights

###  Interactive UI

The dashboard includes separate sections for:

* Dashboard
* Recommendations
* Book & User Analysis
* Model Performance

It also includes responsive navigation, charts, filters, loading states, notifications, and interactive book/user exploration.

##  Recommendation Architecture

The recommendation engine is built around collaborative filtering.

### Item-Based Collaborative Filtering

For a target user, the system:

1. Retrieves books previously rated by the user.
2. Filters for positively rated books.
3. Compares unrated books against the user's rated books.
4. Calculates cosine similarity using users who rated both books.
5. Calculates a similarity-weighted predicted rating.
6. Ranks candidate books.
7. Returns the top recommendations.

Conceptually:

```text
User Ratings
     │
     ▼
Positively Rated Books
     │
     ▼
Book-to-Book Similarity
     │
     ▼
Weighted Predicted Ratings
     │
     ▼
Top-N Recommendations
```

### User-Based Collaborative Filtering

The user-based approach:

1. Builds the target user's rating profile.
2. Calculates similarity with other users.
3. Identifies similar readers.
4. Aggregates ratings from those readers.
5. Adjusts ratings relative to each similar user's average.
6. Predicts ratings for books the target user hasn't rated.
7. Ranks the results.

```text
Target User
     │
     ▼
Compare User Profiles
     │
     ▼
Similar Readers
     │
     ▼
Aggregate Their Ratings
     │
     ▼
Predicted Ratings
     │
     ▼
Top-N Recommendations
```

---

##  Cosine Similarity

The system calculates cosine similarity only over **commonly rated items/users** rather than treating missing ratings as zero.

For vectors `A` and `B`:

```text
cosine_similarity(A, B) =
        A · B
-------------------
     ||A|| ||B||
```

A damping/shrinkage factor is also applied when the number of overlapping ratings is small. This helps reduce artificially strong similarity scores caused by very limited overlap.

---

##  Cold-Start Handling

The application handles users with limited interaction history.

### New user

If a user has no ratings, the system uses a popularity-based fallback.

### Low-activity user

If a user has only a small number of ratings, collaborative filtering is attempted and the recommendation list can be supplemented with highly rated books.

The fallback uses a **Bayesian weighted rating** approach that balances:

* Book average rating
* Number of ratings
* Overall dataset average

This prevents books with only a tiny number of ratings from automatically dominating the recommendations.

---

##  Offline Evaluation

The application performs a hold-out evaluation for users with sufficient rating history.

For eligible users:

1. A positively rated book is held out.
2. The remaining ratings are used as the training data.
3. Recommendations are generated.
4. The held-out book is checked against the Top-K recommendations.

The system reports performance at:

```text
K = 5
K = 10
K = 20
```

It also calculates catalog coverage and the distribution of book-to-book similarity scores.

> **Note:** This is an in-browser/offline evaluation intended for demonstrating and comparing recommendation behavior. It should not be interpreted as a production-grade benchmark without a larger dataset, stronger train/test methodology, and repeated evaluation splits.

---

##  Project Structure

```text
book-recommendation-system/
│
├── public/
│   └── data/
│       ├── books.csv
│       └── ratings.csv
│
├── src/
│   ├── components/
│   │   ├── BookDetails.tsx
│   │   ├── BookSelector.tsx
│   │   ├── BookTable.tsx
│   │   ├── ChartCard.tsx
│   │   ├── RecommendationCard.tsx
│   │   ├── RecommendationList.tsx
│   │   ├── RecommendationMetrics.tsx
│   │   ├── Sidebar.tsx
│   │   ├── UserDetails.tsx
│   │   ├── UserSelector.tsx
│   │   └── ...
│   │
│   ├── pages/
│   │   ├── Dashboard.tsx
│   │   ├── Recommendations.tsx
│   │   ├── BookUserAnalysis.tsx
│   │   └── RecommendationPerformance.tsx
│   │
│   ├── recommendation/
│   │   ├── cosineSimilarity.ts
│   │   ├── itemBasedCF.ts
│   │   ├── userBasedCF.ts
│   │   ├── recommendationEngine.ts
│   │   ├── preprocessing.ts
│   │   └── evaluation.ts
│   │
│   ├── services/
│   │   └── dataset.ts
│   │
│   ├── utils/
│   │   ├── bookAnalysis.ts
│   │   ├── genreAnalysis.ts
│   │   ├── insights.ts
│   │   ├── reportGenerator.ts
│   │   └── userAnalysis.ts
│   │
│   ├── App.tsx
│   ├── main.tsx
│   └── types.ts
│
├── index.html
├── package.json
├── tsconfig.json
├── vite.config.ts
└── .env.example
```

---

##  Tech Stack

| Technology       | Purpose                       |
| ---------------- | ----------------------------- |
| **React 19**     | Frontend UI                   |
| **TypeScript**   | Type-safe application logic   |
| **Vite**         | Development and build tooling |
| **Tailwind CSS** | Styling and responsive UI     |
| **Recharts**     | Data visualization            |
| **PapaParse**    | CSV parsing                   |
| **Lucide React** | Icons                         |
| **Motion**       | UI animations                 |
| **jsPDF**        | PDF report generation         |
| **html2canvas**  | Dashboard/report rendering    |
| **Google GenAI** | AI integration support        |

---

##  Getting Started

### Prerequisites

Make sure you have:

* Node.js
* npm

### 1. Clone the repository

```bash
git clone https://github.com/<your-username>/<your-repository>.git
cd <your-repository>
```

### 2. Install dependencies

```bash
npm install
```

### 3. Configure environment variables

Create a `.env.local` file if your deployment requires Gemini functionality:

```env
GEMINI_API_KEY=your_gemini_api_key
APP_URL=http://localhost:3000
```

### 4. Start the development server

```bash
npm run dev
```

The Vite development server will start the application locally.

---

##  Available Scripts

### Development

```bash
npm run dev
```

Starts the Vite development server.

### Production Build

```bash
npm run build
```

Creates a production build.

### Preview Production Build

```bash
npm run preview
```

Runs the production build locally for preview.

### Type Checking

```bash
npm run lint
```

Runs TypeScript type checking without emitting files.

---

##  Dataset Format

### `books.csv`

The preprocessing pipeline supports fields such as:

```csv
book_id,title,author,genre,publication_year,pages,language
1,The Great Gatsby,F. Scott Fitzgerald,Fiction,1925,180,English
```

Required fields:

* `book_id`
* `title`

Common optional fields:

* `author`
* `genre`
* `publication_year`
* `pages`
* `language`

### `ratings.csv`

Example:

```csv
user_id,book_id,rating,timestamp
1,1,4.5,1700000000
1,2,3.5,1700000100
2,1,5,1700000200
```

The preprocessing layer also recognizes common alternative column names such as:

* `userId`
* `user`
* `bookId`
* `book`
* `score`

Invalid records, duplicate user-book pairs, and ratings that cannot be linked to an existing book are filtered during preprocessing.

##  Data Processing Pipeline

```text
CSV Files
   │
   ▼
CSV Parsing
   │
   ▼
Data Validation & Cleaning
   │
   ├── Books
   └── Ratings
   │
   ▼
User / Book Rating Maps
   │
   ▼
Dataset Statistics
   │
   ▼
Recommendation Engine
   │
   ├── Item-Based CF
   ├── User-Based CF
   └── Popularity Fallback
   │
   ▼
Analytics + Evaluation
   │
   ▼
Interactive Dashboard
```

---

##  Use Cases

This project can be used as a demonstration of:

* Recommendation system design
* Collaborative filtering
* Cosine similarity
* User behavior analysis
* Book/catalog analytics
* Cold-start strategies
* Recommendation evaluation
* Interactive data visualization
* Client-side machine learning concepts

It is particularly suitable as a **machine learning / data science portfolio project** because the recommendation logic, evaluation methodology, data processing, and UI visualization are all exposed within the application.

---

##  Limitations

This project is primarily designed as an interactive demonstration and portfolio application.

Potential areas for improvement include:

* Matrix factorization
* Neural collaborative filtering
* Content-based recommendations using book metadata
* Hybrid recommendation models
* Better train/validation/test splitting
* Cross-validation
* Larger-scale datasets
* Recommendation diversity metrics
* Ranking metrics such as NDCG and MAP
* More robust handling of extremely sparse datasets
* Server-side recommendation computation for large datasets
* Persistent user accounts and rating history

---

##  Future Improvements

Possible extensions include:

* [ ] Hybrid collaborative + content-based recommendations
* [ ] Book embeddings
* [ ] Semantic book similarity
* [ ] Matrix factorization
* [ ] Neural recommendation models
* [ ] User authentication
* [ ] Persistent ratings
* [ ] Recommendation history
* [ ] A/B testing framework
* [ ] Advanced ranking metrics
* [ ] Cloud-hosted recommendation API
* [ ] Larger public book datasets
* [ ] Automated model comparison

---

## 📄 License

Add your preferred license here, for example:

```text
MIT License
```

---

##  Author

**Your Name**

If you found this project useful, consider ⭐ starring the repository.

---

##  Project Highlights

**Book Recommendation System** combines:

> **Collaborative Filtering + Cosine Similarity + Cold-Start Handling + Data Analytics + Offline Evaluation + Interactive Visualization**

into a single web application for exploring and understanding personalized book recommendations.
