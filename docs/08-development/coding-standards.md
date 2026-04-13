# Coding Standards

## Objective
Maintain consistent, readable, and maintainable code across DS-Forge.

## General Principles
- Code must be readable (comments where needed)
- Follow consistent naming conventions
- DRY – Don't Repeat Yourself
- Write tests for critical logic

## Backend Standards (Python + Django)

### Style
- PEP 8 compliant
- Line length: 88 characters (Black default)
- Use Black for auto-formatting
- Use flake8 for linting

### Naming
| Type | Convention | Example |
|------|------------|---------|
| Classes | PascalCase | `UserProfileView` |
| Functions/Variables | snake_case | `calculate_score` |
| Constants | UPPER_SNAKE_CASE | `MAX_RETRY_COUNT` |

### Project Structure

backend/
├── dsforge_backend/
│ ├── settings/
│ │ ├── base.py
│ │ ├── local.py
│ │ └── production.py
│ └── urls.py
├── apps/
│ ├── users/
│ │ ├── models.py
│ │ ├── views.py
│ │ ├── serializers.py
│ │ ├── services.py
│ │ └── urls.py
│ ├── questions/
│ └── evaluation/
└── manage.py


### Views
- Use class-based views (CBV) or viewsets
- Keep views thin – business logic in services.py

### Imports Order
1. Standard library
2. Third-party
3. Django
4. Local apps

## Frontend Standards (React + JavaScript)

### Style
- Prettier for formatting
- ESLint (Airbnb rules)

### Naming
| Type | Convention | Example |
|------|------------|---------|
| Components | PascalCase | `UserProfile.jsx` |
| Hooks | camelCase + `use` prefix | `useAuth.js` |
| Variables/Functions | camelCase | `fetchUserData` |

### Component Structure
```jsx
const UserProfile = ({ userId }) => {
  // hooks
  // event handlers
  return (...);
};
export default UserProfile;
