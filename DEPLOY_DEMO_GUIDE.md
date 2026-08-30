# Project Demo Deployment Guide

> Purpose: Deploy minimal working demos of both projects to prove accountability on resume.
> These are NOT production deployments — they exist to show a reviewer "yes, this person actually built this."

---

## General Rules for Both Demos

### What to SHOW
- Working application frontend (UI loads, buttons work, basic flow completes)
- Architecture diagram (already on portfolio at `/work/`)
- Clean URL (prathameshlonare.me/voting, prathameshlonare.me/dorm-and-dish)

### What NOT to show
- AWS Console access or credentials
- CloudFormation templates or raw IaC files
- Lambda function code (show the result, not the implementation)
- Real user data or test data with personal info
- Billing dashboard or cost information
- API keys, JWT secrets, Cognito pool IDs in frontend code
- Admin panels or backend management interfaces

### Demo Philosophy
A hiring manager clicks the link → sees a working app → clicks around for 30 seconds → confirms "this is real" → moves on. That's it. The demo is proof of work, not a product launch.

---

## Project 1: Serverless Voting Platform

### Live URL
`https://prathameshlonare.me/voting/` (or subdomain: `voting.prathameshlonare.me`)

### What the demo MUST show

| Flow | Why it matters |
|------|----------------|
| Landing page loads with election info | Proves React frontend deployed and serving |
| "Sign Up" / "Login" button visible | Proves Cognito integration exists |
| Candidate cards display with names/images | Proves DynamoDB data retrieval works |
| Cast a vote → confirmation message | Proves Lambda + API Gateway + DynamoDB write path |
| Results page shows vote counts | Proves result aggregation Lambda works |

### What to strip out for demo

| Remove | Reason |
|--------|--------|
| Real college election data | Replace with fake candidates (Candidate A, B, C) |
| Actual student emails | Use test accounts you control |
| CloudWatch dashboard embed | Not needed for demo |
| Admin election creation flow | Not needed — hardcode one active election |
| Rate limiting / abuse protection | Not needed for demo traffic |

### Deployment checklist

```
Frontend (React)
├── Build production bundle (npm run build)
├── Deploy to S3 + CloudFront (or Vercel/Netlify for simplicity)
├── Remove any hardcoded AWS region/account IDs from frontend
├── Ensure candidate images load from S3/CloudFront
└── Test: page loads in <3 seconds, no console errors

Backend (Lambda + API Gateway)
├── Keep CloudFormation stack deployed
├── Seed DynamoDB with 3 fake candidates
├── Create one "active" election with start/end dates in future
├── Test: vote flow end-to-end with test account
└── Verify CloudWatch logs show clean invocations (no errors)

Cognito
├── Create demo user account (demo@prathameshlonare.me or similar)
├── Pre-verify the email so login works immediately
└── Disable sign-up if you don't want random accounts
```

### Minimal test data to seed

```json
// DynamoDB - Elections table
{
  "electionId": "demo-2025",
  "title": "Student Council Election 2025",
  "status": "ACTIVE",
  "startDate": "2025-01-01T00:00:00Z",
  "endDate": "2027-12-31T23:59:59Z"
}

// DynamoDB - Candidates table (3 entries)
[
  { "candidateId": "c1", "name": "Candidate A", "party": "Progress Party", "electionId": "demo-2025" },
  { "candidateId": "c2", "name": "Candidate B", "party": "Unity Alliance", "electionId": "demo-2025" },
  { "candidateId": "c3", "name": "Candidate C", "party": "Future Forward", "electionId": "demo-2025" }
]
```

---

## Project 2: Dorm & Dish Accommodation Platform

### Live URL
`https://prathameshlonare.me/dorm-dish/` (or subdomain: `dormdish.prathameshlonare.me`)

### What the demo MUST show

| Flow | Why it matters |
|------|----------------|
| Landing page loads with platform branding | Proves frontend deployed |
| Browse dorm listings (3-5 items) | Proves DynamoDB read + Lambda query works |
| View dorm detail page | Proves API Gateway routing + Lambda handler |
| Image loads for dorm (from S3/CloudFront) | Proves presigned URL + CDN flow |
| "Book" or "Inquire" button exists (can be non-functional) | Proves UI completeness |

### What to strip out for demo

| Remove | Reason |
|--------|--------|
| Real student data | Replace with fake dorms (Dorm A, B, C) |
| Payment/checkout flow | Not needed — demo ends at "Book" button |
| User registration/login | Disable or use pre-created test accounts |
| Menu/canteen module | Not needed — focus on accommodation only |
| Admin dashboard | Not needed for demo |
| Presigned URL expiry logic | Set long expiry (24h) for demo stability |

### Deployment checklist

```
Frontend
├── Build production bundle
├── Deploy to S3 + CloudFront (or Vercel/Netlify)
├── Ensure dorm images load from CDN
└── Test: browse flow works, no broken images

Backend (Lambda + API Gateway)
├── Keep CloudFormation stack deployed
├── Seed DynamoDB with 3 fake dorm listings
├── Test: listing detail page loads with image
└── Verify CloudWatch logs clean

S3 + CloudFront
├── Upload 3 placeholder dorm images (use free stock photos)
├── Ensure presigned URL generation works
└── Test: images load via CloudFront URL
```

### Minimal test data to seed

```json
// DynamoDB - Dorms table (3 entries)
[
  {
    "dormId": "d1",
    "name": "Sunrise Hostel",
    "location": "Block A, North Campus",
    "capacity": 120,
    "pricePerMonth": 8500,
    "amenities": ["WiFi", "Laundry", "Mess"],
    "imageUrl": "s3://dorm-dish-images/dorm1.jpg"
  },
  {
    "dormId": "d2",
    "name": "Green Valley Residence",
    "location": "Block B, South Campus",
    "capacity": 80,
    "pricePerMonth": 10000,
    "amenities": ["WiFi", "Gym", "Mess", "Parking"],
    "imageUrl": "s3://dorm-dish-images/dorm2.jpg"
  },
  {
    "dormId": "d3",
    "name": "Lakeview Apartments",
    "location": "Block C, East Wing",
    "capacity": 60,
    "pricePerMonth": 12000,
    "amenities": ["WiFi", "Gym", "Pool", "Mess"],
    "imageUrl": "s3://dorm-dish-images/dorm3.jpg"
  }
]
```

---

## Deployment Options (Pick One)

### Option A: AWS Native (shows more skill)
- Frontend: S3 + CloudFront
- Backend: Lambda + API Gateway (already deployed)
- Cost: ~$0-5/month for demo traffic
- Pros: Matches your resume skills exactly
- Cons: More setup time

### Option B: Vercel/Netlify (faster)
- Frontend: Vercel or Netlify (free tier)
- Backend: Keep Lambda + API Gateway on AWS
- Cost: $0
- Pros: Deploy in 5 minutes, auto HTTPS
- Cons: Frontend hosting doesn't match resume tech stack

**Recommendation:** Option B for speed. You can always migrate to S3+CloudFront later. The demo proves the backend works — that's what matters.

---

## Timeline

| Task | Time | Priority |
|------|------|----------|
| Seed DynamoDB with test data (both projects) | 30 min | Do first |
| Deploy Voting frontend (Vercel/Netlify) | 15 min | High |
| Deploy Dorm & Dish frontend | 15 min | High |
| Test both demo flows end-to-end | 15 min | High |
| Update resume with live demo links | 5 min | Last |

**Total: ~75 minutes**

---

## After Deployment

1. Click both links yourself — confirm they load and work
2. Open on your phone — confirm mobile responsive
3. Share link with one friend — ask "does this look real?"
4. Update resume: add `| \href{<url>}{Live Demo}` to both projects

---

## What if something breaks?

- **Lambda errors:** Check CloudWatch logs, usually a permissions issue with test data
- **Images not loading:** Check S3 bucket policy or presigned URL expiry
- **Frontend blank page:** Check browser console — usually a missing environment variable
- **CORS errors:** Check API Gateway CORS configuration matches your frontend domain

Don't spend more than 30 minutes debugging. If it's broken, remove the Live Demo link from the resume and note it as "deployed on request" during interviews.
