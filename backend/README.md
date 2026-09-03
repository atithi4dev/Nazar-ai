## System Architecture (v0)

The drawing below divides the system into **four simple layers**. Each layer shows only the **pipeline name** with the **key technology** used.


```mermaid
flowchart LR
    subgraph 1["1. CAMERAS"]
        C["CCTV / ANPR cameras (IP / RTSP)"]
    end
    subgraph 2["2. AI READS PLATES"]
        P["Detection + OCR<br/>(YOLO + PaddleOCR)"]
    end
    subgraph 3["3. DATA STORE"]
        D["Database + maps<br/>(PostgreSQL + PostGIS)<br/>Cache (Redis) • Files (MinIO/S3)"]
    end
    subgraph 4["4. SERVICES & USERS"]
        S1["Trajectory (route joining)"]
        S2["Traffic analytics"]
        S3["Watchlist alerts"]
        S4["Dashboard<br/>(React + MapLibre)"]
    end
    C1 --> P --> D --> S1 --> S4
    D --> S2 --> S4
    D --> S3 --> S4
```

> Every camera frame flows through the same path. The result splits into **two things officials want**: tracking one plate (for safety) and seeing the whole city's traffic (for planning) — using the same data.

---