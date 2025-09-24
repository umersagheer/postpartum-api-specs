# 🍼 Postpartum App - Backend API Specification

> **Status**: Ready for Implementation  
> **Frontend**: React Native + Expo  
> **Backend**: Node.js/Express (recommended)  
> **Last Updated**: September 2025

This document outlines all API endpoints required by the frontend application. Each service method currently uses mock data and needs to be implemented as actual backend endpoints.

## 📋 Table of Contents
- [Authentication](#authentication)
- [API Endpoints](#api-endpoints)
  - [1. Dashboard Service](#1-dashboard-service)
  - [2. Meal Plans Service](#2-meal-plans-service)
  - [3. Recipe Service](#3-recipe-service)
  - [4. Grocery Service](#4-grocery-service)
  - [5. Onboarding Service](#5-onboarding-service-️-already-implemented)
  - [6. Subscription Service](#6-subscription-service)
- [Implementation Priority](#implementation-priority)
- [Technical Requirements](#technical-requirements)

---

## 🔐 Authentication

**Authentication Provider**: Supabase  
**Authorization**: Bearer Token (JWT) in Authorization header  
**Note**: All endpoints require authenticated user except where specified

```http
Authorization: Bearer <supabase_jwt_token>
Content-Type: application/json
```

---

## 🚀 API Endpoints

### 1. Dashboard Service

> **Priority**: 🔴 High - Core app functionality

#### GET `/api/dashboard`
**Description**: Get user's daily dashboard data including mood, nutrition, and meals  
**Frontend Hook**: `useDashboard(date: string)`  
**Frontend Method**: `dashboardService.getDashboard(request: GetDashboardRequest)`

**Request**
```http
GET /api/dashboard?date=2025-11-17
```

```typescript
interface GetDashboardRequest {
  date: string; // ISO date string (e.g., "2025-11-17")
}
```

**Response** `200 OK`
```typescript
interface DashboardData {
  date: string; // ISO date string
  mood?: MoodLog;
  nutrition: NutritionData;
  meals: DashboardMealsData;
}

interface MoodLog {
  id: string;
  type: 'energized' | 'not_myself' | 'tired' | 'anxious' | 'overwhelmed';
  loggedAt: string; // ISO date string
  recommendations?: MoodRecommendation[];
}

interface MoodRecommendation {
  id: string;
  description: string;
  imageUrl?: string;
}

interface NutritionData {
  calories: {
    consumed: number;
    target: number;
  };
  macros: {
    carbs: { consumed: number; target: number; };
    protein: { consumed: number; target: number; };
    fats: { consumed: number; target: number; };
  };
}

interface DashboardMealsData {
  id?: string;
  breakfast?: MealInfo;
  lunch?: MealInfo;
  dinner?: MealInfo;
  snacks?: MealInfo;
}

interface MealInfo {
  calories: number;
  imageUrl?: string;
  title?: string;
  prepTime?: number; // in minutes
}
```

**Example Response**
```json
{
  "date": "2025-11-17",
  "mood": {
    "id": "mood_123",
    "type": "energized",
    "loggedAt": "2025-11-17T10:30:00Z",
    "recommendations": [
      {
        "id": "rec_1",
        "description": "Great energy! Try a protein-rich snack to maintain it.",
        "imageUrl": "https://example.com/protein-snack.jpg"
      }
    ]
  },
  "nutrition": {
    "calories": { "consumed": 1800, "target": 2200 },
    "macros": {
      "carbs": { "consumed": 200, "target": 275 },
      "protein": { "consumed": 120, "target": 165 },
      "fats": { "consumed": 70, "target": 97 }
    }
  },
  "meals": {
    "breakfast": {
      "calories": 450,
      "imageUrl": "https://example.com/breakfast.jpg",
      "title": "Overnight Oats with Berries",
      "prepTime": 5
    }
  }
}
```

---

#### POST `/api/dashboard/mood`
**Description**: Log user's daily mood  
**Frontend Hook**: `useLogMood()` (mutation)  
**Frontend Method**: `dashboardService.logMood(request: LogMoodRequest)`

**Request**
```http
POST /api/dashboard/mood
Content-Type: application/json
```

```typescript
interface LogMoodRequest {
  date: string; // ISO date string
  moodType: 'energized' | 'not_myself' | 'tired' | 'anxious' | 'overwhelmed';
}
```

**Example Request Body**
```json
{
  "date": "2025-11-17",
  "moodType": "energized"
}
```

**Response** `201 Created`
```typescript
interface LogMoodResponse {
  success: boolean;
  mood: MoodLog;
}
```

---

### 2. Meal Plans Service

> **Priority**: 🔴 High - Core meal planning feature

#### GET `/api/meal-plans`
**Description**: Get user's meal plans for a specific date  
**Frontend Hook**: `useMealPlans(date: string)`  
**Frontend Method**: `mealPlansService.getMealPlans(request: GetMealPlansRequest)`

**Request**
```http
GET /api/meal-plans?date=2025-11-17
```

```typescript
interface GetMealPlansRequest {
  date: string; // ISO date string
}
```

**Response** `200 OK`
```typescript
interface MealPlansData {
  breakfast: Recipe[];
  lunch: Recipe[];
  dinner: Recipe[];
}

interface Recipe {
  id: string;
  title: string;
  imageUrl: string;
  calories: number;
  prepTime: number; // in minutes
  category: 'breakfast' | 'lunch' | 'dinner' | 'snacks';
  description: string;
}
```

**Example Response**
```json
{
  "breakfast": [
    {
      "id": "recipe_001",
      "title": "Overnight Oats with Berries",
      "imageUrl": "https://example.com/overnight-oats.jpg",
      "calories": 450,
      "prepTime": 5,
      "category": "breakfast",
      "description": "Creamy overnight oats topped with fresh berries and nuts"
    }
  ],
  "lunch": [...],
  "dinner": [...]
}
```

---

### 3. Recipe Service

> **Priority**: 🔴 High - Recipe details and user interactions

#### GET `/api/recipes/{recipeId}`
**Description**: Get detailed recipe information  
**Frontend Hook**: `useRecipeDetail(recipeId: string)`  
**Frontend Method**: `recipeService.getRecipeDetail(recipeId: string)`

**Request**
```http
GET /api/recipes/recipe_001
```interface RecipeDetail {
  id: string;
  title: string;
  imageUrl: string;
  calories: number;
  prepTime: number;
  mealType: 'breakfast' | 'lunch' | 'dinner' | 'snacks';
  nutrition: RecipeNutrition;
  nutritionTargets?: NutritionTargets;
  instructions: string[];
  ingredients: RecipeIngredient[];
  isFavorited: boolean;
  eatenToday: boolean;
  skippedToday: boolean;
}

interface RecipeNutrition {
  protein: number;
  fats: number;
  carbs: number;
}

interface NutritionTargets {
  protein: number;
  fats: number;
  carbs: number;
}

interface RecipeIngredient {
  name: string;
  unit: string;
  amount: number;
}
```

---

#### POST `/api/recipes/{recipeId}/favorite`
**Description**: Toggle recipe favorite status  
**Frontend Hook**: `useToggleFavorite()` (mutation)  
**Frontend Method**: `recipeService.toggleFavorite(recipeId: string)`
**Request**
```http
POST /api/recipes/recipe_001/favorite

**Response** `200 OK`
```typescript
interface ToggleFavoriteResponse {
  isFavorited: boolean;
}
```

---

#### POST `/api/recipes/{recipeId}/eaten`
**Description**: Mark recipe as eaten for today  
**Frontend Hook**: `useMarkAsEaten()` (mutation)  
**Frontend Method**: `recipeService.markAsEaten(recipeId: string)`

**Request**
```http
POST /api/recipes/recipe_001/eaten
```

**Response** `204 No Content`

---

#### POST `/api/recipes/{recipeId}/skipped`
**Description**: Mark recipe as skipped for today  
**Frontend Hook**: `useMarkAsSkipped()` (mutation)  
**Frontend Method**: `recipeService.markAsSkipped(recipeId: string)`

**Request**
```http
POST /api/recipes/recipe_001/skipped
```

**Response** `204 No Content`

---

### 4. Grocery Service

> **Priority**: 🟡 Medium - Grocery list management

#### POST `/api/grocery/add-ingredients`
**Description**: Add recipe ingredients to user's grocery list  
**Frontend Hook**: `useAddToGroceryList()` (mutation)  
**Frontend Method**: `groceryService.addToGroceryList(request: AddToGroceryListRequest)`

**Request**
```http
POST /api/grocery/add-ingredients
Content-Type: application/json
```

```typescript
interface AddToGroceryListRequest {
  recipeId: string;
  ingredientIds: string[];
}
```

**Example Request Body**
```json
{
  "recipeId": "recipe_001",
  "ingredientIds": ["ing_001", "ing_002", "ing_003"]
}
```

**Response** `200 OK`
```typescript
interface AddToGroceryListResponse {
  success: boolean;
  addedCount: number;
  message: string;
}
```

---

### 5. Onboarding Service ⚠️ **ALREADY IMPLEMENTED**

> **Status**: ✅ Complete - These endpoints are already implemented and working

#### GET `/api/onboarding/questions`
**Frontend Hook**: `useOnboardingQuestions()`  
**Frontend Method**: `onboardingService.getQuestions()`

#### POST `/api/onboarding/calories-intake`
**Frontend Hook**: `useCalculateCalories()` (mutation)  
**Frontend Method**: `onboardingService.calculateCaloriesIntake(answers: Record<string, OnboardingAnswer>)`

#### POST `/api/onboarding/submit/batch`
**Frontend Hook**: `useSubmitOnboardingBatch()` (mutation)  
**Frontend Method**: `onboardingService.submitOnboardingBatch(answers: Record<string, OnboardingAnswer>)`

---

### 6. Subscription Service

> **Priority**: 🟡 Medium - Monetization

#### GET `/api/subscription/plans`
**Description**: Get available subscription plans  
**Frontend Hook**: `useSubscriptionPlans()`  
**Frontend Method**: `subscriptionService.getPlans()`

**Request**
```http
GET /api/subscription/plans
```

**Response** `200 OK`
```typescript
interface SubscriptionPlansResponse {
  plans: PlanDto[];
  features: string[];
}

interface PlanDto {
  id: string;
  title: string;
  price: string;
  subtitle: string;
  badge?: string;
}
```

---

## 🎯 Implementation Priority

### 🔴 High Priority (Implement First)
1. **Dashboard Service** - Essential for main app functionality
2. **Meal Plans Service** - Core feature for meal planning  
3. **Recipe Service** - Required for recipe details and user interactions

### 🟡 Medium Priority (Implement Second)
4. **Grocery Service** - Nice-to-have feature for grocery list management
5. **Subscription Service** - Required for monetization

### ⚪ Low Priority (Optional)
6. **Onboarding Service** - Already implemented ✅

---

## 🛠 Technical Requirements

### Base URL
```
Development: http://localhost:3000/api
Production: https://your-api-domain.com/api
```

### Request/Response Format
- **Content-Type**: `application/json`
- **Character Encoding**: UTF-8
- **Date Format**: ISO 8601 (e.g., `2025-11-17T10:30:00Z`)

### Error Handling
All endpoints should return appropriate HTTP status codes:

```typescript
interface ErrorResponse {
  error: {
    code: string;
    message: string;
    details?: any;
  };
  timestamp: string;
}
```

**Common Status Codes**:
- `200 OK` - Success
- `201 Created` - Resource created
- `204 No Content` - Success, no response body
- `400 Bad Request` - Invalid request data
- `401 Unauthorized` - Missing or invalid token
- `404 Not Found` - Resource not found
- `500 Internal Server Error` - Server error

### Frontend Configuration
- **HTTP Client**: Axios with interceptors
- **Caching**: TanStack Query (5-minute stale time)
- **Error Handling**: Centralized error boundary
- **Configuration File**: `/libs/configs/api-client.ts`

### File Structure Reference
```
frontend/
├── libs/
│   ├── services/          # Service layer (API calls)
│   ├── queries/           # TanStack Query hooks  
│   ├── interfaces/        # TypeScript types
│   ├── constants/api.ts   # API route constants
│   └── configs/api-client.ts  # Axios configuration
└── stores/
    ├── auth.store.ts      # Authentication state
    └── onboarding.store.ts # Onboarding state
```

---

## 📞 Questions & Support

**Frontend Developer**: [Your Name]  
**GitHub Issues**: Use this repository's issues for questions and clarifications  
**Slack/Discord**: [Your communication channel]

---

## 📝 Changelog

| Date       | Version | Changes                                                                                                                                                                                                               |
| ---------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 2025-09-22 | 1.0.0   | Initial API specification                                                                                                                                                                                             |
| 2025-09-24 | 1.1.0   | **Updated Recipe Details Type** - Changed structure of `RecipeDetail` to include `mealType`, `nutritionTargets`, and new structure for `RecipeIngredient` which now includes `name`, `unit`, and `amount` properties. |


---


## Updated Recipe Details Type

interface RecipeDetail {
  id: string;
  title: string;
  imageUrl: string;
  calories: number;
  prepTime: number;
  mealType: 'breakfast' | 'lunch' | 'dinner' | 'snacks';
  nutrition: RecipeNutrition;
  nutritionTargets?: NutritionTargets;
  instructions: string[];
  ingredients: RecipeIngredient[];
  isFavorited: boolean;
  eatenToday: boolean;
  skippedToday: boolean;
}


**Happy Coding! 🚀**
