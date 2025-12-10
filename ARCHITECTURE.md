# البنية المعمارية - Architecture

## Overview
Research Assistant Pro is a full-stack web application designed to help academic researchers prepare high-quality research papers with professional formatting and academic references.

## Technology Stack

### Frontend
- **Framework**: React 18+ with Next.js
- **Language**: TypeScript
- **Styling**: TailwindCSS
- **Rich Text Editor**: Lexical
- **RTL Support**: Full Arabic language support
- **State Management**: React Context API + Custom Hooks
- **HTTP Client**: Axios

### Backend
- **Platform**: Firebase
  - **Database**: Firestore (NoSQL)
  - **Authentication**: Firebase Auth
  - **Hosting**: Firebase Hosting
  - **Functions**: Cloud Functions
  - **Storage**: Cloud Storage

### External APIs
- **Crossref API**: Journal articles and DOI metadata
- **OpenAlex API**: Academic papers and citations
- **Google Docs API**: Document export and formatting
- **PDF Export**: pdfkit or similar library

## Project Structure

```
research-assistant-pro/
├── src/
│   ├── app/
│   │   ├── page.tsx          # Home page
│   │   ├── layout.tsx        # Root layout
│   │   └── globals.css       # Global styles
│   ├── components/
│   │   ├── Editor/
│   │   │   ├── RichTextEditor.tsx
│   │   │   ├── SectionToolbar.tsx
│   │   │   └── WordCounter.tsx
│   │   ├── ReferencesManager/
│   │   │   ├── ReferencesList.tsx
│   │   │   ├── AddReferenceForm.tsx
│   │   │   ├── SearchAcademicDB.tsx
│   │   │   └── CitationPreview.tsx
│   │   ├── ProjectSetup/
│   │   │   ├── InitialForm.tsx
│   │   │   └── TemplateSelector.tsx
│   │   ├── ExportPanel/
│   │   │   ├── ExportOptions.tsx
│   │   │   └── FormatSelector.tsx
│   │   ├── Navigation/
│   │   │   ├── Sidebar.tsx
│   │   │   └── TopBar.tsx
│   │   └── Common/
│   │       ├── Button.tsx
│   │       ├── Modal.tsx
│   │       └── LoadingSpinner.tsx
│   ├── pages/
│   │   ├── api/
│   │   │   ├── crossref.ts
│   │   │   ├── openalex.ts
│   │   │   ├── export-pdf.ts
│   │   │   └── export-docx.ts
│   │   ├── editor/[projectId].tsx
│   │   ├── projects.tsx
│   │   └── settings.tsx
│   ├── hooks/
│   │   ├── useEditor.ts
│   │   ├── useReferences.ts
│   │   ├── useProjects.ts
│   │   ├── useWordCount.ts
│   │   └── useAuth.ts
│   ├── utils/
│   │   ├── firebase.ts
│   │   ├── api-clients.ts
│   │   ├── citation-formatter.ts
│   │   ├── word-counter.ts
│   │   ├── export-handler.ts
│   │   └── validation.ts
│   ├── services/
│   │   ├── firestore.service.ts
│   │   ├── auth.service.ts
│   │   ├── references.service.ts
│   │   └── export.service.ts
│   ├── types/
│   │   ├── research.types.ts
│   │   ├── reference.types.ts
│   │   └── user.types.ts
│   └── constants/
│       ├── specializations.ts
│       ├── citation-formats.ts
│       └── section-templates.ts
├── public/
│   ├── fonts/
│   └── images/
├── styles/
│   └── globals.css
├── firebase/
│   ├── config.ts
│   └── functions/
│       ├── generatePDF/
│       ├── generateDOCX/
│       └── validateReferences/
├── .env.example
├── .eslintrc.json
├── next.config.js
├── tailwind.config.js
└── tsconfig.json
```

## Core Features Architecture

### 1. Project Initialization
```
User Input -> Project Form -> Firebase Store -> UI State Update
```

### 2. Rich Text Editing
```
Editor Component -> Lexical Instance -> Content Storage -> Auto-save to Firebase
```

### 3. References Management
```
Search Query -> API Call (Crossref/OpenAlex) -> Parse Results 
-> Store in Firebase -> Display in Manager -> Insert into Editor
```

### 4. Word Counting
```
Editor Content -> Count Algorithm -> Display Progress -> Validate Target
```

### 5. Citation Formatting
```
Selected Reference -> Format Selector -> Generate Citation String -> Insert at Cursor
```

### 6. Export Pipeline
```
Editor Content + References + Metadata -> Export Service
-> Format as PDF/DOCX -> Google Docs API/Download
```

## Data Models

### Research Project
```typescript
interface ResearchProject {
  id: string;
  title: string;
  researchType: 'graduation' | 'conference';
  specialization: 'education' | 'social_work' | 'islamic_sciences';
  citationStyle: 'apa' | 'chicago' | 'harvard' | 'arabic_apa';
  targetWordCount: number;
  university: string;
  department: string;
  createdAt: Timestamp;
  updatedAt: Timestamp;
  userId: string;
  content: {
    [sectionKey: string]: string;
  };
}
```

### Reference
```typescript
interface Reference {
  id: string;
  projectId: string;
  author: string[];
  title: string;
  sourceType: 'journal' | 'book' | 'book_chapter' | 'thesis' | 'website';
  publicationYear: number;
  doi?: string;
  url?: string;
  journal?: string;
  volume?: string;
  issue?: string;
  pages?: string;
  keywords: string[];
  createdAt: Timestamp;
}
```

## Firebase Rules

### Firestore Security
- Users can only access their own projects
- References linked to projects are protected by project ownership
- Anonymous uploads not allowed

### Authentication
- Email/Password
- Google OAuth (future)
- Email verification required

## API Integration

### Crossref API
- Search journals and DOI metadata
- Free API, no authentication needed
- Query: `/works?query=title,author&rows=20`

### OpenAlex API
- Comprehensive academic search
- Filter by specialization/field
- Query: `/works?search=title&filter=type:journal-article`

## Performance Considerations

1. **Auto-save**: Debounced saves every 5 seconds to Firestore
2. **Code Splitting**: Lazy load editor and export components
3. **Image Optimization**: Use Next.js Image component
4. **Caching**: Cache API results locally
5. **Database Indexing**: Index `userId`, `projectId` in Firestore

## Security

1. Environment variables for API keys
2. Server-side validation of all inputs
3. CORS policies configured
4. Rate limiting on API calls
5. SQL injection prevention (NoSQL, but still validate)

## Deployment

- **Hosting**: Firebase Hosting / Vercel
- **CI/CD**: GitHub Actions
- **Database**: Firestore (auto-scaling)
- **CDN**: Firebase Hosting CDN
