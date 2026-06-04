# CLDV6211-A-PART2-2026
This repo was created because the classroom repo was not working. 





# EventEase2026

**Student:** Muhammad Yusuf Peer | **Student Number:** ST10440378
**Module:** CLDV6211A — Cloud Development A | **Submission:** Part 3

**Live Azure URL:** https://eventease2026-1044-e5hub3d8hzdkgnbu.southafricanorth-01.azurewebsites.net/
**YouTube Video:** https://youtu.be/BjWSTVhyVks
---

## What Is EventEase2026

EventEase2026 is an ASP.NET Core MVC venue booking system built for booking specialists to manage venues, events, and bookings. It is deployed live on Microsoft Azure using Azure App Service, Azure SQL Database, and Azure Blob Storage.

---

## Features

### Venues
- Create, read, update, and delete venues
- Upload venue images to Azure Blob Storage (venue-images container)
- Images displayed on the Venues index page
- Delete restriction — venues with active bookings cannot be deleted
- Filter by name, location, event type, date range, and availability

### Events
- Create, read, update, and delete events
- Assign EventType from predefined lookup table (Conference, Expo, Workshop, Wedding, Concert, Corporate Function)
- Upload event images to Azure Blob Storage
- Delete restriction — events with active bookings cannot be deleted
- Filter by event type, date range, and availability

### Bookings
- Create, read, update, and delete bookings
- Consolidated view joining Booking + Venue + Event + EventType data
- Search by Booking ID, booking reference, or event name
- Filter by event type, date range, and availability
- Available venues panel shown when date range is selected
- Double-booking prevention — overlapping venue/date combinations are blocked with a clear error message

### Advanced Filtering
- EventType, date range, and availability filters work together simultaneously on Venues, Events, and Bookings pages

### Image Storage
- Images stored in Azure Blob Storage, not on the server
- Only JPG, PNG, GIF, and WEBP accepted
- GUID-based blob names prevent naming conflicts

---

## Project Structure

```
EventEase2026/
├── Controllers/
│   ├── HomeController.cs
│   ├── VenuesController.cs
│   ├── EventsController.cs
│   └── BookingsController.cs
├── Models/
│   ├── Venue.cs
│   ├── Event.cs
│   ├── EventType.cs
│   ├── Booking.cs
│   ├── VenueIndexViewModel.cs
│   ├── EventIndexViewModel.cs
│   └── BookingIndexViewModel.cs
├── Views/
│   ├── Venues/       (Index, Create, Edit, Delete, Details)
│   ├── Events/       (Index, Create, Edit, Delete, Details)
│   ├── Bookings/     (Index, Create, Edit, Delete, Details)
│   └── Home/         (Index)
├── Data/
│   ├── ApplicationDbContext.cs
│   └── DbInitializer.cs
├── Services/
│   ├── IImageStorageService.cs
│   └── AzureBlobImageStorageService.cs
├── Migrations/
│   ├── InitialCreate
│   ├── RestrictEventBookingDelete
│   └── AddEventTypesAndAdvancedFilters
└── Deployment/
    └── azure-sql-migration.sql
```

---

## Database Design

| Table | Key Columns |
|---|---|
| Venues | VenueId (PK), Name, Location, Capacity, ImageUrl |
| EventTypes | EventTypeId (PK), Name |
| Events | EventId (PK), Name, StartDate, EndDate, ImageUrl, VenueId (FK), EventTypeId (FK) |
| Bookings | BookingId (PK), BookingRef, CustomerName, CustomerEmail, VenueId (FK), EventId (FK) |

**Relationships:**
- One Venue → many Events (one-to-many)
- One Venue → many Bookings (one-to-many, DeleteBehavior.Restrict)
- One EventType → many Events (one-to-many)
- One Event → many Bookings (one-to-many, DeleteBehavior.Restrict)

---

## Azure Components

| Service | Purpose |
|---|---|
| Azure App Service | Hosts the ASP.NET Core MVC application |
| Azure SQL Database | Stores all relational data (venues, events, event types, bookings) |
| Azure Blob Storage | Stores venue and event images in the venue-images container |

---

## Local Setup

1. Clone the repository
2. Open `EventEase2026.sln` in Visual Studio 2022
3. Ensure the startup project is `EventEase2026`
4. Update `appsettings.json` with your local SQL Server connection string and Azure Storage connection string
5. Run the application — migrations are applied automatically on startup

**Local appsettings.json:**
```json
"ConnectionStrings": {
  "DefaultConnection": "Server=.\\SQLEXPRESS;Database=EventEase2026Db;Trusted_Connection=True;Encrypt=False;",
  "AzureStorage": "DefaultEndpointsProtocol=https;AccountName=...;AccountKey=...;EndpointSuffix=core.windows.net"
},
"AzureStorage": {
  "ContainerName": "venue-images"
}
```

---

## Azure App Service Configuration

Set these in **App Service → Settings → Environment variables:**

**Connection strings tab:**

| Name | Type | Value |
|---|---|---|
| DefaultConnection | SQLAzure | Azure SQL connection string |
| AzureStorage | Custom | Azure Storage connection string |

**App settings tab:**

| Name | Value |
|---|---|
| AzureStorage__ContainerName | venue-images |

After setting variables: click **Apply → Confirm → Restart**.

---

## Migrations

| Migration | Description |
|---|---|
| InitialCreate | Creates Venues, Events, and Bookings tables |
| RestrictEventBookingDelete | Sets DeleteBehavior.Restrict on Booking foreign keys |
| AddEventTypesAndAdvancedFilters | Adds EventTypes table, EventTypeId to Events, seeds 6 predefined categories |

---

## Seeded Data

On first run the following is seeded automatically:

**EventTypes:** Conference, Expo, Workshop, Wedding, Concert, Corporate Function

**Venues:** Durban ICC (5000 capacity, Durban), Cape Town Expo Hall (3000 capacity, Cape Town)

**Events:** Business Summit 2026 — Conference, Tech Expo 2026 — Expo

**Bookings:** One sample booking for John Smith at Durban ICC

---

