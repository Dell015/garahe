# Garahe — Learning Roadmap
### Learn the stack by building the actual project. Nothing gets thrown away.

> Every step in this roadmap produces something that stays in Garahe. You are not doing exercises — you are building the real thing, one concept at a time.

---

## How to use this roadmap

Each step has four parts:

- **What you build** — the exact thing you will have working by the end
- **What you learn** — the concept the step teaches
- **How it connects to Garahe** — why this step is not throwaway practice
- **The checkpoint** — a specific thing you can see, click, or test that proves it works

Do not move to the next step until the checkpoint passes. If it does not pass, that is the learning — figure out why, fix it, then move on. Breaking things and fixing them is the entire job.

---

## Before you start — install these once

| Tool | What it is | Where to get it |
|---|---|---|
| VS Code | Code editor | code.visualstudio.com |
| Node.js 20+ | Runs JavaScript/TypeScript locally | nodejs.org |
| Python 3.11+ | Runs your backend locally | python.org |
| Git | Version control | git-scm.com |
| GitHub account | Remote code storage | github.com |
| Supabase account | Your database and auth | supabase.com |
| Vercel account | Frontend hosting | vercel.com |
| Railway account | Backend hosting | railway.app |

Install VS Code extensions: Python, Pylance, ESLint, Tailwind CSS IntelliSense, GitLens.

---

## Stage 0 — Understand the tools before touching them

**Time estimate: 1 day of reading, not coding.**

Before writing a single line, understand what each tool is and what problem it solves. Read these descriptions and make sure you can explain each one without looking.

**Python** is the language your backend is written in. It runs on a server and handles logic — checking if a user is allowed to do something, reading from the database, calling the AI. It does not run in the browser.

**TypeScript** is the language your frontend is written in. It runs in the browser and handles what the user sees and interacts with. It talks to the backend by making HTTP requests.

**Next.js** is a framework built on top of TypeScript/React. It gives you routing (different URLs show different pages), and a project structure to follow.

**FastAPI** is a framework built on top of Python. It lets you define endpoints — URLs that your frontend can call to get data or trigger actions.

**Supabase** is a managed service that gives you a PostgreSQL database, user authentication, and file storage. You do not manage a database server — Supabase does that.

**PostgreSQL** is the actual database. It stores everything — users, vehicles, service entries. It is a relational database — data lives in tables with rows and columns that reference each other.

**Vercel** hosts your Next.js frontend. Push code to GitHub, Vercel detects it and deploys automatically.

**Railway** hosts your FastAPI backend. Same idea — push code, it deploys.

The relationship between all of them:

```
Browser (user)
    ↕ HTTP requests
Next.js on Vercel (frontend)
    ↕ HTTP requests
FastAPI on Railway (backend)
    ↕ SQL queries
PostgreSQL on Supabase (database)
```

When you can explain this diagram to someone who has never coded, move to Stage 1.

---

## Stage 1 — Python basics
### "Print Hello World and understand why it works"

---

### Step 1.1 — Your first Python file

**What you build:** A Python file that prints text to the terminal.

**What you learn:** How Python runs, what a file is, what print() does.

Create a folder on your computer called `garahe`. Inside it, create `hello.py`:

```python
print("Hello, Garahe")
```

Open a terminal in that folder. Run:

```
python hello.py
```

**Checkpoint:** You see `Hello, Garahe` in the terminal.

**How it connects to Garahe:** Every Python file in your FastAPI backend starts the same way — Python reads the file top to bottom and executes what it finds.

---

### Step 1.2 — Variables and data types

**What you build:** A Python file that describes a vehicle.

**What you learn:** Variables, strings, integers, dictionaries, f-strings.

```python
vehicle_make = "Toyota"
vehicle_model = "Innova"
vehicle_year = 2021
current_odometer = 45000

vehicle = {
    "make": vehicle_make,
    "model": vehicle_model,
    "year": vehicle_year,
    "odometer": current_odometer
}

print(vehicle)
print(f"This is a {vehicle['year']} {vehicle['make']} {vehicle['model']}")
print(f"Current mileage: {vehicle['odometer']} km")
```

**Checkpoint:** The terminal prints the dictionary and both formatted strings correctly.

**How it connects to Garahe:** Every vehicle in your database will be represented as a dictionary exactly like this.

---

### Step 1.3 — Functions

**What you build:** A function that computes whether a service is due.

**What you learn:** Defining and calling functions, parameters, return values, if/else.

```python
def is_service_due(last_service_km, current_km, interval_km):
    km_since_service = current_km - last_service_km
    km_remaining = interval_km - km_since_service

    if km_remaining <= 0:
        return {"status": "attention", "km_overdue": abs(km_remaining)}
    elif km_remaining <= 500:
        return {"status": "upcoming", "km_remaining": km_remaining}
    else:
        return {"status": "good", "km_remaining": km_remaining}

oil_check = is_service_due(
    last_service_km=34000,
    current_km=54200,
    interval_km=10000
)

print(oil_check)
```

**Checkpoint:** The function correctly returns `"attention"` with `km_overdue: 10200`.

**How it connects to Garahe:** This exact function — or something very close to it — is what your FastAPI backend runs every time it calculates the Status Dashboard. You just wrote real Garahe business logic.

---

### Step 1.4 — Lists and loops

**What you build:** A function that checks all components of a vehicle at once.

**What you learn:** Lists, for loops.

```python
def is_service_due(last_service_km, current_km, interval_km):
    km_since = current_km - last_service_km
    km_remaining = interval_km - km_since
    if km_remaining <= 0:
        return "attention"
    elif km_remaining <= 500:
        return "upcoming"
    return "good"

components = [
    {"name": "Engine Oil", "last_service_km": 44000, "interval_km": 5000},
    {"name": "Air Filter", "last_service_km": 30000, "interval_km": 15000},
    {"name": "Fuel Filter", "last_service_km": 34000, "interval_km": 20000},
    {"name": "Front Brake Pads", "last_service_km": 20000, "interval_km": 30000},
]

current_odometer = 54200

for component in components:
    status = is_service_due(
        component["last_service_km"],
        current_odometer,
        component["interval_km"]
    )
    print(f"{component['name']}: {status}")
```

**Checkpoint:** Each component prints with the correct status.

**How it connects to Garahe:** This loop is what powers the Status Dashboard — iterating over a vehicle's components and computing each status. The only difference in the real app is that the components come from the database instead of a hardcoded list.

---

### Step 1.5 — Virtual environments and packages

**What you build:** A virtual environment with your first installed package.

**What you learn:** What a virtual environment is, why it matters, pip.

A virtual environment is an isolated Python installation for a specific project. It prevents packages from one project conflicting with another.

```
python -m venv venv

# Activate (Mac/Linux)
source venv/bin/activate

# Activate (Windows)
venv\Scripts\activate
```

You should see `(venv)` at the start of your terminal prompt. Now install a package:

```
pip install requests
pip list
```

Create a `requirements.txt` file:

```
requests
```

**Checkpoint:** `pip list` shows `requests` installed. You see `(venv)` in your terminal. You understand why you never install packages without activating the virtual environment first.

**How it connects to Garahe:** Your FastAPI backend has a `requirements.txt` with every package it needs. Anyone who clones the project runs `pip install -r requirements.txt` to get an identical environment.

---

## Stage 2 — FastAPI basics
### "Make Python talk to the web"

---

### Step 2.1 — Your first FastAPI server

**What you build:** A running web server with one endpoint.

**What you learn:** What a server is, what an endpoint is, what HTTP GET means.

```
pip install fastapi uvicorn
pip freeze > requirements.txt
```

Create `main.py`:

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def root():
    return {"message": "Garahe is running"}

@app.get("/health")
def health_check():
    return {"status": "ok", "app": "Garahe"}
```

Run it:

```
uvicorn main:app --reload
```

Open your browser at `http://localhost:8000`. Then `http://localhost:8000/health`. Then `http://localhost:8000/docs` — this is FastAPI's automatic interactive documentation. Every endpoint you create appears here. You can test your API without writing any frontend code.

**Checkpoint:** JSON responses appear in the browser for both URLs. Both endpoints are visible and testable at `/docs`.

**How it connects to Garahe:** This is the exact setup your Garahe backend starts with. The `/health` endpoint stays in the project forever.

---

### Step 2.2 — Path parameters and returning data

**What you build:** Endpoints that return a vehicle list and a specific vehicle by ID.

**What you learn:** Path parameters, HTTP 404, returning structured data.

```python
from fastapi import FastAPI, HTTPException

app = FastAPI()

fake_vehicles = {
    "1": {"id": "1", "make": "Toyota", "model": "Innova", "year": 2021, "odometer": 54200},
    "2": {"id": "2", "make": "Kawasaki", "model": "Z650", "year": 2020, "odometer": 34200},
}

@app.get("/vehicles")
def list_vehicles():
    return list(fake_vehicles.values())

@app.get("/vehicles/{vehicle_id}")
def get_vehicle(vehicle_id: str):
    vehicle = fake_vehicles.get(vehicle_id)
    if not vehicle:
        raise HTTPException(status_code=404, detail="Vehicle not found")
    return vehicle
```

**Checkpoint:** `/vehicles` returns both vehicles. `/vehicles/1` returns the Innova. `/vehicles/99` returns a 404 with "Vehicle not found."

**How it connects to Garahe:** These are the first two real API endpoints. In Stage 4, you replace the fake dictionary with real database queries.

---

### Step 2.3 — POST requests and Pydantic validation

**What you build:** An endpoint that accepts a new vehicle and validates the input automatically.

**What you learn:** HTTP POST, request bodies, Pydantic models.

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from typing import Optional
import uuid

app = FastAPI()

class VehicleCreate(BaseModel):
    make: str
    model: str
    year: int
    variant: Optional[str] = None
    current_odometer: int

class Vehicle(VehicleCreate):
    id: str

fake_vehicles: dict[str, Vehicle] = {}

@app.get("/vehicles")
def list_vehicles():
    return list(fake_vehicles.values())

@app.post("/vehicles", status_code=201)
def create_vehicle(data: VehicleCreate):
    vehicle_id = str(uuid.uuid4())
    vehicle = Vehicle(id=vehicle_id, **data.model_dump())
    fake_vehicles[vehicle_id] = vehicle
    return vehicle
```

Go to `/docs`, find POST `/vehicles`, click "Try it out," and submit a new vehicle.

**Checkpoint:** Submitting a valid vehicle returns 201 with the created vehicle. Submitting without a required field returns a validation error automatically — you did not write that error handling, Pydantic did it for you.

**How it connects to Garahe:** This Pydantic pattern — one model for input, one for output — is used for every single resource in the Garahe API.

---

### Step 2.4 — CORS configuration

**What you build:** A FastAPI server that accepts requests from a different port.

**What you learn:** What CORS is and why it exists.

CORS (Cross-Origin Resource Sharing) is a browser security rule. When your Next.js frontend at `localhost:3000` calls your FastAPI backend at `localhost:8000`, the browser blocks it because they are on different ports. You must tell FastAPI to allow it explicitly.

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI()

app.add_middleware(
    CORSMiddleware,
    allow_origins=[
        "http://localhost:3000",
        "https://garahe.vercel.app",
    ],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

@app.get("/health")
def health():
    return {"status": "ok"}
```

**Checkpoint:** You understand what CORS is and why this middleware is needed. You will never forget to add it after being blocked by a CORS error the first time.

**How it connects to Garahe:** This exact configuration goes into the Garahe backend permanently. When you deploy to Railway, you add the Vercel URL to `allow_origins`.

---

## Stage 3 — TypeScript and Next.js basics
### "Build the interface and make it talk to the backend"

---

### Step 3.1 — Create the Next.js project

**What you build:** A running Next.js app with a custom homepage.

**What you learn:** What Next.js is, how to scaffold a project, how the App Router works.

```
npx create-next-app@latest frontend --typescript --tailwind --app --no-src-dir
cd frontend
npm run dev
```

Open `http://localhost:3000`. You see the default Next.js homepage.

Open `frontend/app/page.tsx`. Delete everything and replace with:

```tsx
export default function HomePage() {
  return (
    <main className="min-h-screen bg-gray-950 text-white flex items-center justify-center">
      <div className="text-center">
        <h1 className="text-4xl font-bold text-white mb-2">Garahe</h1>
        <p className="text-gray-400">Your garage. Your story. Privately yours.</p>
      </div>
    </main>
  )
}
```

**Checkpoint:** You see your own Garahe homepage — dark background, title, subtitle. The default Next.js content is gone.

**How it connects to Garahe:** This is the actual Garahe homepage. It grows from here.

---

### Step 3.2 — TypeScript types and interfaces

**What you build:** A TypeScript file that defines the shape of Garahe's data.

**What you learn:** What TypeScript adds to JavaScript, interfaces, union types.

Create `frontend/types/vehicle.ts`:

```typescript
export interface Vehicle {
  id: string
  make: string
  model: string
  year: number
  variant: string | null
  current_odometer: number
  health_score: number
}

export interface ServiceEntry {
  id: string
  vehicle_id: string
  title: string
  description: string
  odometer_at_entry: number
  entry_date: string
  performed_by: string
  total_cost: number
}

export type ComponentStatus = "good" | "upcoming" | "attention"

export interface VehicleComponent {
  id: string
  vehicle_id: string
  name: string
  display_name: string
  status: ComponentStatus
  last_serviced_km: number
  service_interval_km: number
}
```

**Checkpoint:** The file saves without TypeScript errors. You understand why `string | null` means "either a string or null." You understand why `ComponentStatus` is a union type — TypeScript will warn you if you try to assign anything else to it.

**How it connects to Garahe:** These exact types are used throughout the Garahe frontend. Every piece of data fetched from the API is typed against these interfaces.

---

### Step 3.3 — Fetch data from the backend

**What you build:** A Next.js page that calls your FastAPI backend and displays vehicles.

**What you learn:** fetch, async/await, useEffect, useState, loading and error states.

Make sure your FastAPI backend is still running on port 8000.

Create `frontend/app/vehicles/page.tsx`:

```tsx
"use client"

import { useEffect, useState } from "react"
import { Vehicle } from "@/types/vehicle"

export default function VehiclesPage() {
  const [vehicles, setVehicles] = useState<Vehicle[]>([])
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState<string | null>(null)

  useEffect(() => {
    fetch("http://localhost:8000/vehicles")
      .then((res) => {
        if (!res.ok) throw new Error("Failed to fetch vehicles")
        return res.json()
      })
      .then((data) => {
        setVehicles(data)
        setLoading(false)
      })
      .catch((err) => {
        setError(err.message)
        setLoading(false)
      })
  }, [])

  if (loading) return <p className="text-white p-8">Loading your garage...</p>
  if (error) return <p className="text-red-400 p-8">Error: {error}</p>

  return (
    <main className="min-h-screen bg-gray-950 p-8">
      <h1 className="text-2xl font-bold text-white mb-6">My Garage</h1>
      {vehicles.length === 0 ? (
        <p className="text-gray-400">No vehicles yet.</p>
      ) : (
        <ul className="space-y-4">
          {vehicles.map((vehicle) => (
            <li
              key={vehicle.id}
              className="bg-gray-900 rounded-lg p-4 border border-gray-800"
            >
              <p className="text-white font-medium">
                {vehicle.year} {vehicle.make} {vehicle.model}
              </p>
              <p className="text-gray-400 text-sm">
                {vehicle.current_odometer.toLocaleString()} km
              </p>
            </li>
          ))}
        </ul>
      )}
    </main>
  )
}
```

Open `http://localhost:3000/vehicles`.

**Checkpoint:** The page shows the vehicles from your FastAPI fake database. If the backend is off, you see the error state. You understand what useEffect, useState, loading, and error each do.

**How it connects to Garahe:** This is the Garage page. The loading state, error state, and empty state patterns are used on every data-fetching page in the app.

---

### Step 3.4 — Routing and navigation

**What you build:** A vehicle detail page with its own URL, linked from the list.

**What you learn:** Next.js file-based routing, dynamic segments, useParams, Link.

Create `frontend/app/vehicles/[id]/page.tsx`:

```tsx
"use client"

import { useEffect, useState } from "react"
import { useParams } from "next/navigation"
import { Vehicle } from "@/types/vehicle"

export default function VehicleDetailPage() {
  const params = useParams()
  const vehicleId = params.id as string
  const [vehicle, setVehicle] = useState<Vehicle | null>(null)
  const [loading, setLoading] = useState(true)

  useEffect(() => {
    fetch(`http://localhost:8000/vehicles/${vehicleId}`)
      .then((res) => res.json())
      .then((data) => {
        setVehicle(data)
        setLoading(false)
      })
  }, [vehicleId])

  if (loading) return <p className="text-white p-8">Loading...</p>
  if (!vehicle) return <p className="text-red-400 p-8">Vehicle not found.</p>

  return (
    <main className="min-h-screen bg-gray-950 p-8">
      <h1 className="text-2xl font-bold text-white mb-2">
        {vehicle.year} {vehicle.make} {vehicle.model}
      </h1>
      <p className="text-gray-400 mb-6">
        {vehicle.current_odometer.toLocaleString()} km
      </p>
      <div className="bg-gray-900 rounded-lg p-6 border border-gray-800">
        <p className="text-gray-400 text-sm mb-1">Health Score</p>
        <p className="text-3xl font-bold text-white">
          {vehicle.health_score ?? "—"}
        </p>
      </div>
    </main>
  )
}
```

Update the vehicles list to link to each detail page by importing `Link from "next/link"` and wrapping each list item.

**Checkpoint:** Clicking a vehicle in the list navigates to `/vehicles/1` and shows that vehicle's detail. The URL changes. Back button works.

**How it connects to Garahe:** The file `app/vehicles/[id]/page.tsx` is the exact real file path in the Garahe project. You just built the routing structure the entire app follows.

---

### Step 3.5 — Forms

**What you build:** A form that creates a new vehicle by calling the FastAPI POST endpoint.

**What you learn:** Controlled inputs, form submission, POST with JSON body, redirect after success.

Create `frontend/app/vehicles/new/page.tsx`:

```tsx
"use client"

import { useState } from "react"
import { useRouter } from "next/navigation"

export default function NewVehiclePage() {
  const router = useRouter()
  const [submitting, setSubmitting] = useState(false)
  const [error, setError] = useState<string | null>(null)
  const [form, setForm] = useState({
    make: "",
    model: "",
    year: new Date().getFullYear(),
    variant: "",
    current_odometer: 0,
  })

  function handleChange(e: React.ChangeEvent<HTMLInputElement>) {
    const { name, value, type } = e.target
    setForm((prev) => ({
      ...prev,
      [name]: type === "number" ? Number(value) : value,
    }))
  }

  async function handleSubmit(e: React.FormEvent) {
    e.preventDefault()
    setSubmitting(true)
    setError(null)
    try {
      const res = await fetch("http://localhost:8000/vehicles", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(form),
      })
      if (!res.ok) throw new Error("Failed to create vehicle")
      const vehicle = await res.json()
      router.push(`/vehicles/${vehicle.id}`)
    } catch (err) {
      setError((err as Error).message)
      setSubmitting(false)
    }
  }

  const fields = [
    { label: "Make", name: "make", type: "text", placeholder: "Toyota" },
    { label: "Model", name: "model", type: "text", placeholder: "Innova" },
    { label: "Variant", name: "variant", type: "text", placeholder: "2.8 G AT" },
    { label: "Year", name: "year", type: "number", placeholder: "2021" },
    { label: "Current Odometer (km)", name: "current_odometer", type: "number", placeholder: "45000" },
  ]

  return (
    <main className="min-h-screen bg-gray-950 p-8 max-w-lg mx-auto">
      <h1 className="text-2xl font-bold text-white mb-8">Add a vehicle</h1>
      <form onSubmit={handleSubmit} className="space-y-4">
        {fields.map((field) => (
          <div key={field.name}>
            <label className="block text-gray-400 text-sm mb-1">{field.label}</label>
            <input
              name={field.name}
              type={field.type}
              placeholder={field.placeholder}
              value={form[field.name as keyof typeof form]}
              onChange={handleChange}
              className="w-full bg-gray-900 border border-gray-700 rounded-lg px-4 py-2 text-white placeholder-gray-600 focus:outline-none focus:border-gray-500"
            />
          </div>
        ))}
        {error && <p className="text-red-400 text-sm">{error}</p>}
        <button
          type="submit"
          disabled={submitting}
          className="w-full bg-white text-gray-950 font-medium py-2 rounded-lg hover:bg-gray-100 disabled:opacity-50 transition-colors"
        >
          {submitting ? "Adding..." : "Add vehicle"}
        </button>
      </form>
    </main>
  )
}
```

**Checkpoint:** Filling in the form and submitting creates a vehicle, then navigates to its detail page. The new vehicle appears in the list. You understand why `e.preventDefault()` is needed.

**How it connects to Garahe:** This is the real Add Vehicle form. The same pattern — controlled inputs, submit handler, POST to API, redirect on success — is used for every form in the app.

---

## Stage 4 — Real database
### "Replace fake data with real data"

---

### Step 4.1 — Connect FastAPI to Supabase Postgres

**What you build:** FastAPI connected to a real Postgres database.

**What you learn:** Database connections, environment variables, never hardcoding secrets.

```
pip install sqlalchemy psycopg2-binary python-dotenv alembic
pip freeze > requirements.txt
```

Create `.env` in your backend folder:

```
DATABASE_URL=postgresql://postgres:[PASSWORD]@[HOST]:5432/postgres
```

Get this from: Supabase dashboard → Project Settings → Database → Connection string (URI). Never commit `.env` to Git — add it to `.gitignore` immediately.

Create `database.py`:

```python
import os
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker
from dotenv import load_dotenv

load_dotenv()

DATABASE_URL = os.getenv("DATABASE_URL")
engine = create_engine(DATABASE_URL)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
Base = declarative_base()

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

**Checkpoint:** `python -c "from database import engine; print(engine)"` runs without error. You understand why the database URL comes from an environment variable.

---

### Step 4.2 — SQLAlchemy models and Alembic migrations

**What you build:** The vehicles table created in Supabase by your Python code.

**What you learn:** SQLAlchemy ORM, Alembic migrations, the relationship between Python classes and database tables.

Create `models.py`:

```python
import uuid
import enum
from sqlalchemy import Column, String, Integer, DateTime, Enum
from sqlalchemy.dialects.postgresql import UUID
from sqlalchemy.sql import func
from database import Base

class VehicleType(str, enum.Enum):
    car = "car"
    motorcycle = "motorcycle"
    truck = "truck"
    van = "van"

class Vehicle(Base):
    __tablename__ = "vehicles"

    id = Column(UUID(as_uuid=True), primary_key=True, default=uuid.uuid4)
    owner_id = Column(UUID(as_uuid=True), nullable=False)
    make = Column(String, nullable=False)
    model = Column(String, nullable=False)
    variant = Column(String, nullable=True)
    year = Column(Integer, nullable=False)
    vehicle_type = Column(Enum(VehicleType), default=VehicleType.car)
    current_odometer = Column(Integer, default=0)
    health_score = Column(Integer, default=0)
    created_at = Column(DateTime(timezone=True), server_default=func.now())
    updated_at = Column(DateTime(timezone=True), onupdate=func.now())
```

Initialize Alembic and run the migration:

```
alembic init alembic
alembic revision --autogenerate -m "create vehicles table"
alembic upgrade head
```

**Checkpoint:** Open Supabase dashboard → Table Editor. You see a `vehicles` table with the exact columns you defined in Python. You created a database table by writing Python — not by clicking in a UI.

---

### Step 4.3 — Replace fake data with real queries

**What you build:** The vehicles API reading from and writing to the real database.

**What you learn:** SQLAlchemy queries, dependency injection in FastAPI.

```python
from fastapi import FastAPI, HTTPException, Depends
from fastapi.middleware.cors import CORSMiddleware
from sqlalchemy.orm import Session
from pydantic import BaseModel
from typing import Optional

from database import get_db
from models import Vehicle, VehicleType

app = FastAPI()

app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:3000"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

FAKE_OWNER_ID = "00000000-0000-0000-0000-000000000001"

class VehicleCreate(BaseModel):
    make: str
    model: str
    year: int
    variant: Optional[str] = None
    vehicle_type: VehicleType = VehicleType.car
    current_odometer: int = 0

@app.get("/vehicles")
def list_vehicles(db: Session = Depends(get_db)):
    return db.query(Vehicle).filter(Vehicle.owner_id == FAKE_OWNER_ID).all()

@app.post("/vehicles", status_code=201)
def create_vehicle(data: VehicleCreate, db: Session = Depends(get_db)):
    vehicle = Vehicle(owner_id=FAKE_OWNER_ID, **data.model_dump())
    db.add(vehicle)
    db.commit()
    db.refresh(vehicle)
    return vehicle

@app.get("/vehicles/{vehicle_id}")
def get_vehicle(vehicle_id: str, db: Session = Depends(get_db)):
    vehicle = db.query(Vehicle).filter(
        Vehicle.id == vehicle_id,
        Vehicle.owner_id == FAKE_OWNER_ID
    ).first()
    if not vehicle:
        raise HTTPException(status_code=404, detail="Vehicle not found")
    return vehicle
```

**Checkpoint:** Add a vehicle using the Next.js form. Refresh the Supabase dashboard — the row is there. Restart the FastAPI server. The vehicle is still there. Data now persists across restarts. The `FAKE_OWNER_ID` is the only temporary piece — real auth replaces it in Stage 5.

---

## Stage 5 — Authentication
### "Make the app know who you are"

---

### Step 5.1 — Supabase Auth in Next.js

**What you build:** Sign-up and sign-in pages using Supabase Auth.

**What you learn:** What authentication is, how sessions work, Supabase Auth SDK.

```
npm install @supabase/supabase-js @supabase/ssr
```

Create `frontend/lib/supabase.ts`:

```typescript
import { createBrowserClient } from "@supabase/ssr"

export function createClient() {
  return createBrowserClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!
  )
}
```

Create `frontend/.env.local`:

```
NEXT_PUBLIC_SUPABASE_URL=https://[your-project].supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=[your-anon-key]
```

Get both values from Supabase dashboard → Project Settings → API.

Build a login page that calls `supabase.auth.signInWithPassword()` and a signup page that calls `supabase.auth.signUp()`.

**Checkpoint:** You can sign up and sign in. Supabase dashboard → Authentication → Users shows your account.

---

### Step 5.2 — Send the auth token to FastAPI and verify it

**What you build:** A system where every API request carries proof of who the user is, and the backend verifies it.

**What you learn:** Bearer tokens, JWT verification, replacing the fake owner ID.

Every fetch call in Next.js gets the session first and attaches the token:

```typescript
const supabase = createClient()
const { data: { session } } = await supabase.auth.getSession()

const res = await fetch("http://localhost:8000/vehicles", {
  headers: {
    "Authorization": `Bearer ${session?.access_token}`,
    "Content-Type": "application/json",
  },
})
```

In FastAPI, write an auth dependency:

```python
pip install supabase
```

```python
import os
from fastapi import Header, HTTPException
from supabase import create_client

supabase_client = create_client(
    os.getenv("SUPABASE_URL"),
    os.getenv("SUPABASE_SERVICE_KEY")
)

async def get_current_user(authorization: str = Header(...)):
    if not authorization.startswith("Bearer "):
        raise HTTPException(status_code=401, detail="Invalid header")
    token = authorization.replace("Bearer ", "")
    try:
        user = supabase_client.auth.get_user(token)
        return user.user
    except Exception:
        raise HTTPException(status_code=401, detail="Invalid token")
```

Replace `FAKE_OWNER_ID` in every endpoint:

```python
@app.get("/vehicles")
def list_vehicles(
    db: Session = Depends(get_db),
    current_user = Depends(get_current_user)
):
    return db.query(Vehicle).filter(
        Vehicle.owner_id == current_user.id
    ).all()
```

**Checkpoint:** Sign in, the vehicles page shows only your vehicles. Create a second account — it sees zero vehicles. Add a vehicle on the second account — it appears only for that account. Users cannot see each other's data.

**How it connects to Garahe:** Authentication and data isolation are complete. This is one of the most critical security properties in the app.

---

## Stage 6 — Service entries and the Timeline
### "Log real maintenance data"

At this point you know all the patterns. Stage 6 applies them to the core data model.

**What you build:** Complete service entry system — create, read, list by component, search.

**What you learn:** Foreign keys in SQLAlchemy, filtering, text search.

Add to `models.py`:

```python
from sqlalchemy import ForeignKey, Text
from sqlalchemy.dialects.postgresql import JSONB
from sqlalchemy.orm import relationship

class ServiceEntry(Base):
    __tablename__ = "service_entries"

    id = Column(UUID(as_uuid=True), primary_key=True, default=uuid.uuid4)
    vehicle_id = Column(UUID(as_uuid=True), ForeignKey("vehicles.id"), nullable=False)
    component_id = Column(UUID(as_uuid=True), ForeignKey("components.id"), nullable=True)
    title = Column(String, nullable=False)
    description = Column(Text)
    odometer_at_entry = Column(Integer)
    entry_date = Column(DateTime(timezone=True))
    performed_by = Column(String)
    parts_used = Column(JSONB, default=list)
    total_cost = Column(Integer, default=0)
    receipt_url = Column(String, nullable=True)
    created_at = Column(DateTime(timezone=True), server_default=func.now())

    vehicle = relationship("Vehicle", back_populates="service_entries")
```

Text search in SQLAlchemy:

```python
entries = db.query(ServiceEntry).filter(
    ServiceEntry.vehicle_id == vehicle_id,
    ServiceEntry.description.ilike(f"%{search_term}%")
).order_by(ServiceEntry.entry_date.desc()).all()
```

**Checkpoint:** You can log a service entry for a vehicle, see it in the Timeline, and search for it by keyword.

---

## Stage 7 — Deployment
### "Make it live on the internet"

---

### Step 7.1 — Deploy the backend to Railway

Create a `Procfile` in the backend folder:

```
web: uvicorn main:app --host 0.0.0.0 --port $PORT
```

Push your backend to GitHub. Go to Railway → New Project → Deploy from GitHub repo. Add environment variables in Railway's dashboard (DATABASE_URL, SUPABASE_URL, SUPABASE_SERVICE_KEY). Deploy.

**Checkpoint:** `https://your-app.up.railway.app/health` returns `{"status": "ok"}` in your browser.

---

### Step 7.2 — Deploy the frontend to Vercel

Replace all hardcoded `localhost:8000` URLs in Next.js with an environment variable:

```typescript
const API_URL = process.env.NEXT_PUBLIC_API_URL || "http://localhost:8000"

fetch(`${API_URL}/vehicles`)
```

Connect the frontend repo to Vercel. Add environment variables in Vercel's dashboard. Deploy. Update FastAPI's CORS `allow_origins` to include your Vercel URL. Redeploy the backend.

**Checkpoint:** Your Vercel URL loads the full app. Sign in, add a vehicle, log a service entry — all working from a URL anyone can open. Phase 1a-b is live.

---

## Stage 8 — Status Dashboard and health score
### "Compute what needs attention"

Apply the `is_service_due` function from Step 1.3 — now against real database data. Write a FastAPI endpoint `GET /vehicles/{id}/status` that queries all components, computes each status, calculates the weighted health score, and returns the three-category dashboard structure.

Build the Status Dashboard page in Next.js — three sections (Recently Maintained, Coming Up, Look Into This), color-coded component cards, health score prominently displayed.

**Checkpoint:** The Status Dashboard shows accurately computed statuses for all components based on real logged service entries and real odometer data.

---

## Stage 9 — File uploads
### "Attach receipts and photos"

Enable Supabase Storage and create buckets for receipts and workshop photos. Write a FastAPI endpoint that accepts a file upload, sends it to Supabase Storage, and returns the URL. Store the URL in the service entry. Add a file input to the service entry form in Next.js.

**Checkpoint:** You can attach a receipt photo to a service entry and see it displayed in the Timeline.

---

## Stage 10 — Trust Layer
### "Share history without losing control"

Build access code generation, the public mechanic view (no login required), the entry submission and approval flow, and ownership transfer.

The new concept is database transactions — multiple operations that must all succeed or all fail:

```python
try:
    vehicle.owner_id = new_owner_id
    vehicle.purchase_price = None
    db.commit()
except Exception:
    db.rollback()
    raise HTTPException(status_code=500, detail="Transfer failed")
```

**Checkpoint:** Phase 1 is fully complete. Every feature in the Phase 1 definition of done works on the deployed, live app.

---

## Stage 11 — Three.js and the 3D viewer
### "Make the car visual and interactive"

Start this stage only after you have been using the Phase 1 app and have real data in it.

```
npm install three @react-three/fiber @react-three/drei
npm install --save-dev @types/three
```

Work through the React Three Fiber "getting started" example before touching any vehicle model: load a cube, add orbit controls (click-drag to rotate), change its color. Understand the scene, camera, and lighting model.

Then source a GLB car/motorcycle model from Sketchfab's free section. Open it in Blender (free), rename mesh groups to match your component zone names (front_wheel_zone, engine_bay, etc.), export as GLB.

Build the VehicleViewer component in stages:
1. Load and display the model
2. Add orbit controls
3. Add zone detection on hover
4. Add click handler that opens a component panel
5. Apply color-coded materials based on real component status from the API
6. Connect to the log entry form

**Checkpoint:** The 3D model rotates. Zones are color-coded based on real component data. Clicking a zone opens the correct component panel. Logging an entry updates the zone color immediately without a page reload.

---

## Stage 12 — The AI layer
### "Make the app understand what you describe"

```
pip install anthropic
```

The key piece is building the system prompt with the vehicle's real component history as context:

```python
import anthropic
import os

client = anthropic.Anthropic(api_key=os.getenv("ANTHROPIC_API_KEY"))

def build_system_prompt(vehicle, components):
    component_lines = "\n".join([
        f"- {c.display_name}: last serviced at {c.last_serviced_km} km, "
        f"interval every {c.service_interval_km} km, status: {c.status}"
        for c in components
    ])

    return f"""You are a vehicle maintenance assistant for Garahe.

The user is asking about their {vehicle.year} {vehicle.make} {vehicle.model}.
Current odometer: {vehicle.current_odometer} km.

Component history:
{component_lines}

When the user describes a symptom, identify the most likely component and explain why,
referencing their specific maintenance history. Be honest about uncertainty.

Respond in this exact JSON format:
{{
  "response": "your plain-language response",
  "flagged_component_name": "component_name or null",
  "highlight_zone": "3d_zone_name or null",
  "proposed_entry": {{
    "title": "short title",
    "component_name": "component_name",
    "description": "what to log"
  }}
}}
If no entry should be proposed, set proposed_entry to null."""
```

Write the chat endpoint in FastAPI. Build the chat panel in Next.js. Connect `highlight_zone` in the AI response to the VehicleViewer's highlight state. Add a "Log this" confirmation card that appears when the AI proposes an entry — one tap approves it and creates the real entry.

**Checkpoint:** Typing "I hear a grinding noise when I brake" gets a response that references your actual brake service history, highlights the front wheel zone on the 3D model, and offers to log a "needs attention" entry you can approve in one tap.

---

## You are done with the core build.

By Stage 12, you have learned and used:

| Skill | Where you learned it |
|---|---|
| Python fundamentals | Stage 1 |
| REST API design with FastAPI | Stage 2 |
| TypeScript fundamentals | Stage 3 |
| React and Next.js | Stages 3–4 |
| Relational database design | Stage 4 |
| SQLAlchemy ORM | Stages 4–6 |
| Alembic migrations | Stage 4 |
| JWT authentication | Stage 5 |
| File uploads and storage | Stage 9 |
| Database transactions | Stage 10 |
| 3D graphics in the browser (Three.js) | Stage 11 |
| LLM API integration | Stage 12 |
| Prompt engineering | Stage 12 |
| Full-stack deployment | Stage 7 |

Every skill came from building something that is now part of Garahe. Nothing was throwaway practice. Everything is in production.

---

*Garahe learning roadmap — June 2026*
