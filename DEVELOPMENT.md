# دليل التطوير - Development Guide

## Prerequisites

### Required
- Node.js 18+ and npm/yarn
- Git
- Firebase Account (free tier available)
- Code Editor (VS Code recommended)

### Optional
- Docker (for containerization)
- Vercel CLI (for deployment)

## Getting Started

### 1. Clone the Repository
```bash
git clone https://github.com/yourusername/research-assistant-pro
cd research-assistant-pro
```

### 2. Install Dependencies
```bash
npm install
# or
yarn install
```

### 3. Environment Setup

Create a `.env.local` file in the root directory:

```env
# Firebase Config
NEXT_PUBLIC_FIREBASE_API_KEY=your_api_key
NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN=your_auth_domain
NEXT_PUBLIC_FIREBASE_PROJECT_ID=your_project_id
NEXT_PUBLIC_FIREBASE_STORAGE_BUCKET=your_storage_bucket
NEXT_PUBLIC_FIREBASE_MESSAGING_SENDER_ID=your_sender_id
NEXT_PUBLIC_FIREBASE_APP_ID=your_app_id

# API Keys
CROSSREF_API_KEY=optional_key
OPENALEX_API_KEY=optional_key

# App Config
NEXT_PUBLIC_APP_URL=http://localhost:3000
NODE_ENV=development
```

### 4. Firebase Setup

1. Go to [Firebase Console](https://console.firebase.google.com)
2. Create a new project: "research-assistant-pro"
3. Enable Authentication (Email/Password)
4. Create a Firestore database
5. Copy your config to `.env.local`

### 5. Start Development Server
```bash
npm run dev
# The app will be available at http://localhost:3000
```

## Project Structure Details

### Key Directories

#### `/src/components`
Reusable UI components organized by feature:
- `Editor/`: Rich text editor components
- `ReferencesManager/`: Citation and reference management
- `ProjectSetup/`: Initial configuration forms
- `ExportPanel/`: Export functionality UI
- `Navigation/`: Layout and navigation components

#### `/src/hooks`
Custom React hooks for business logic:
```typescript
// useEditor.ts - Editor state and operations
const { content, updateContent, getWordCount } = useEditor(projectId)

// useReferences.ts - Reference management
const { references, addReference, deleteReference } = useReferences(projectId)

// useWordCount.ts - Dynamic word counting
const { totalWords, sectionWords, progress } = useWordCount(content, targetCount)
```

#### `/src/services`
Business logic and external API integrations:
- `firestore.service.ts`: Database operations
- `references.service.ts`: Reference CRUD and search
- `export.service.ts`: PDF/DOCX generation

#### `/src/utils`
Utility functions:
- `citation-formatter.ts`: Convert references to citations in APA/Chicago/Harvard
- `word-counter.ts`: Accurate word counting algorithm
- `api-clients.ts`: Crossref and OpenAlex API clients

## Component Examples

### Rich Text Editor
```typescript
// src/components/Editor/RichTextEditor.tsx
import { LexicalComposer } from '@lexical/react/LexicalComposer'
import { RichTextPlugin } from '@lexical/react/LexicalRichTextPlugin'

export const RichTextEditor: React.FC<EditorProps> = ({ onContentChange }) => {
  return (
    <LexicalComposer initialConfig={initialConfig}>
      <RichTextPlugin
        contentEditable={<ContentEditable />}
        placeholder={<Placeholder />}
        ErrorBoundary={LexicalErrorBoundary}
      />
    </LexicalComposer>
  )
}
```

### References Manager
```typescript
// src/components/ReferencesManager/ReferencesList.tsx
export const ReferencesList: React.FC = () => {
  const { references, deleteReference } = useReferences()
  
  return (
    <div className="references-container">
      {references.map(ref => (
        <ReferenceItem
          key={ref.id}
          reference={ref}
          onDelete={() => deleteReference(ref.id)}
        />
      ))}
    </div>
  )
}
```

## API Endpoints

### Internal API Routes

#### Search References
```
GET /api/search
Query: ?query=keyword&source=crossref|openalex&specialization=education
Response: { results: Reference[] }
```

#### Export to PDF
```
POST /api/export/pdf
Body: { projectId, format: 'apa'|'chicago' }
Response: { downloadUrl: string }
```

#### Export to DOCX
```
POST /api/export/docx
Body: { projectId, format: 'apa'|'chicago' }
Response: { downloadUrl: string }
```

## Testing

### Unit Tests
```bash
npm run test
```

### E2E Tests
```bash
npm run test:e2e
```

## Code Style

### ESLint
```bash
npm run lint
npm run lint:fix
```

### Type Checking
```bash
npm run type-check
```

## Building for Production

```bash
npm run build
npm start
```

## Deployment

### Firebase Hosting
```bash
npm run build
npm install -g firebase-tools
firebase login
firebase deploy
```

### Vercel
```bash
npm i -g vercel
vercel --prod
```

## Database Schema

### Firestore Collections

#### `/users/{userId}`
```typescript
{
  email: string
  displayName: string
  createdAt: Timestamp
  updatedAt: Timestamp
  preferences: {
    theme: 'light' | 'dark'
    defaultSpecialization: string
    defaultCitationStyle: string
  }
}
```

#### `/projects/{projectId}`
```typescript
{
  title: string
  description: string
  userId: string
  researchType: 'graduation' | 'conference'
  specialization: string
  citationStyle: string
  targetWordCount: number
  university: string
  department: string
  sections: {
    introduction: string
    methodology: string
    // ... other sections
  }
  createdAt: Timestamp
  updatedAt: Timestamp
  status: 'draft' | 'completed'
}
```

#### `/references/{referenceId}`
```typescript
{
  projectId: string
  author: string[]
  title: string
  sourceType: string
  publicationYear: number
  doi?: string
  url?: string
  journalName?: string
  volume?: string
  issue?: string
  pages?: string
  keywords: string[]
  doi?: string
  createdAt: Timestamp
}
```

## Common Issues

### Firebase Connection Fails
- Verify `.env.local` has correct credentials
- Check Firebase Console for active project
- Clear browser cache and restart dev server

### Editor Content Not Saving
- Check browser console for errors
- Verify Firestore rules allow writes
- Check network tab for failed requests

### Word Count Inaccurate
- Ensure text nodes are properly updated
- Check word-counter utility logic
- Test with different content types

## Contributing

1. Create a feature branch: `git checkout -b feature/your-feature`
2. Make changes and commit: `git commit -am 'Add feature'`
3. Push to branch: `git push origin feature/your-feature`
4. Submit a Pull Request

## Resources

- [Next.js Documentation](https://nextjs.org/docs)
- [Firebase Documentation](https://firebase.google.com/docs)
- [Lexical Documentation](https://lexical.dev)
- [TailwindCSS Documentation](https://tailwindcss.com)
- [TypeScript Handbook](https://www.typescriptlang.org/docs)

## Support

For issues and questions:
- Open a GitHub Issue
- Check existing documentation
- Contact: dev@research-pro.com
