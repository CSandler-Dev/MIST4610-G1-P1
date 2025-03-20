# MIST4610 Group Project 1

## Team Name and Members

### Group 1
- Caleb Sandler (cjs06002@uga.edu)
- Nikita Brahmane [@nikitabrahmane](https://github.com/nikitabrahmane)
- Jimmy Vu (jnv31487@uga.edu)
- John Housman (jah88867@uga.edu)

## Scenario Description

Our client is a volleyball club that manages players, coaches, teams, venues, courts, tournaments, and practices. The club is experiencing rapid growth with over 200 players across junior, high school, and adult divisions. They needed a database to replace their inefficient system of spreadsheets and paper records to track:
- Player team assignments and memberships
- Practice scheduling across multiple venues
- Tournament registrations and payment tracking
- Court bookings and availability
- Emergency contact information for safety compliance

## Data Model

![Volleyball Club Data Model](dm-ss.png)

Our data model consists of 10 main entities with relationships that track all aspects of the volleyball club operations. Players belong to teams, which are led by coaches. Teams participate in tournaments, practice on courts within venues, and players maintain membership plans. The model efficiently tracks attendance, emergency contacts, and tournament registrations.

## Data Dictionary

### Table: Players
| Column Name | Description | Data Type | Size | Format | Key? |
|-------------|-------------|-----------|------|--------|------|
| player_id | Unique identifier for each player | INT | - | - | PK |
| name | Player's full name | VARCHAR | 100 | - | - |
| email | Player's email address | VARCHAR | 100 | - | - |
| phone | Player's contact number | VARCHAR | 20 | - | - |
| birth_date | Player's date of birth | DATE | - | YYYY-MM-DD | - |
| team_id | Team the player belongs to | INT | - | - | FK (Teams) |
| join_date | Date player joined the club | DATE | - | YYYY-MM-DD | - |

---

## Table: Coaches
| Column Name | Description | Data Type | Size | Format | Key? |
|-------------|-------------|-----------|------|--------|------|
| coach_id | Unique identifier for each coach | INT | - | - | PK |
| name | Coach's full name | VARCHAR | 100 | - | - |
| email | Coach's email address | VARCHAR | 100 | - | - |
| phone | Coach's contact number | VARCHAR | 20 | - | - |
| certification | Coaching certification details | VARCHAR | 255 | - | - |

---

## Table: Teams
| Column Name | Description | Data Type | Size | Format | Key? |
|-------------|-------------|-----------|------|--------|------|
| team_id | Unique identifier for each team | INT | - | - | PK |
| team_name | Name of the team | VARCHAR | 100 | - | - |
| coach_id | Coach assigned to the team | INT | - | - | FK (Coaches) |
| division | Age group or skill division of the team | VARCHAR | 50 | - | - |

---

## Table: Venues
| Column Name | Description | Data Type | Size | Format | Key? |
|-------------|-------------|-----------|------|--------|------|
| venue_id | Unique identifier for each venue | INT | - | - | PK |
| name | Venue name | VARCHAR | 100 | - | - |
| address | Venue location | VARCHAR | 255 | - | - |
| capacity | Maximum occupancy of the venue | INT | - | - | - |

---

## Table: Courts
| Column Name | Description | Data Type | Size | Format | Key? |
|-------------|-------------|-----------|------|--------|------|
| court_id | Unique identifier for each court | INT | - | - | PK |
| venue_id | Venue where the court is located | INT | - | - | FK (Venues) |
| court_number | Court designation within the venue | INT | - | - | - |

---

## Table: Tournaments
| Column Name | Description | Data Type | Size | Format | Key? |
|-------------|-------------|-----------|------|--------|------|
| tournament_id | Unique identifier for each tournament | INT | - | - | PK |
| name | Tournament name | VARCHAR | 100 | - | - |
| venue_id | Venue where the tournament is hosted | INT | - | - | FK (Venues) |
| dates | Tournament schedule | DATE | - | YYYY-MM-DD | - |

---

## Table: Team_Registrations
| Column Name | Description | Data Type | Size | Format | Key? |
|-------------|-------------|-----------|------|--------|------|
| registration_id | Unique identifier for each registration | INT | - | - | PK |
| tournament_id | Tournament being registered for | INT | - | - | FK (Tournaments) |
| team_id | Team participating in the tournament | INT | - | - | FK (Teams) |
| date | Date of registration | DATE | - | YYYY-MM-DD | - |

---

## Table: Practice_Sessions
| Column Name | Description | Data Type | Size | Format | Key? |
|-------------|-------------|-----------|------|--------|------|
| session_id | Unique identifier for each practice session | INT | - | - | PK |
| team_id | Team attending the practice session | INT | - | - | FK (Teams) |
| court_id | Court assigned for the session | INT | - | - | FK (Courts) |
| date | Date of the practice session | DATE | - | YYYY-MM-DD | - |

---

## Table: Player_Attendance
| Column Name | Description | Data Type | Size | Format | Key? |
|-------------|-------------|-----------|------|--------|------|
| attendance_id | Unique identifier for each attendance record | INT | - | - | PK |
| player_id | Player who attended the session | INT | - | - | FK (Players) |
| session_id | Practice session attended | INT | - | - | FK (Practice_Sessions) |
| status | Attendance status (Present/Absent) | VARCHAR | 10 | - | - |

---

## Table: Membership_Plans
| Column Name | Description | Data Type | Size | Format | Key? |
|-------------|-------------|-----------|------|--------|------|
| plan_id | Unique identifier for each membership plan | INT | - | - | PK |
| name | Membership plan name | VARCHAR | 100 | - | - |
| duration | Duration of the membership (e.g., 3 months, 1 year) | VARCHAR | 50 | - | - |
| fee | Cost of the membership plan | DECIMAL | 10,2 | - | - |

---

## Table: Player_Memberships
| Column Name | Description | Data Type | Size | Format | Key? |
|-------------|-------------|-----------|------|--------|------|
| membership_id | Unique identifier for each player membership | INT | - | - | PK |
| player_id | Player assigned to the membership plan | INT | - | - | FK (Players) |
| plan_id | Membership plan chosen | INT | - | - | FK (Membership_Plans) |
| start_date | Start date of membership | DATE | - | YYYY-MM-DD | - |
| end_date | End date of membership | DATE | - | YYYY-MM-DD | - |

---

## Table: Player_Emergency_Contacts
| Column Name | Description | Data Type | Size | Format | Key? |
|-------------|-------------|-----------|------|--------|------|
| contact_id | Unique identifier for each emergency contact | INT | - | - | PK |
| player_id | Player associated with the contact | INT | - | - | FK (Players) |
| name | Emergency contact name | VARCHAR | 100 | - | - |
| phone | Emergency contact number | VARCHAR | 20 | - | - |
| relationship | Relationship to the player | VARCHAR | 50 | - | - |

## Queries

### Query 1: Player Attendance Analysis

**Description**: Identifies players who have missed practices and calculates their attendance rates.

**Justification**: Helps coaches identify players who need follow-up and monitors team commitment levels. Low attendance may indicate scheduling conflicts or waning interest that could affect team performance.

```sql
SELECT p.player_id,
    p.name AS player_name,
    t.team_name,
    c.name AS coach_name,
    COUNT(pa.attendance_id) AS total_practices,
    SUM(CASE WHEN pa.status = 'Present' THEN 1 ELSE 0 END) AS practices_attended,
    ROUND((SUM(CASE WHEN pa.status = 'Present' THEN 1 ELSE 0 END) / COUNT(pa.attendance_id)) * 100, 2) AS attendance_percentage
FROM Players p
JOIN Teams t ON p.team_id = t.team_id
JOIN Coaches c ON t.coach_id = c.coach_id
JOIN Player_Attendance pa ON p.player_id = pa.player_id
GROUP BY p.player_id, p.name, t.team_name, c.name
HAVING SUM(CASE WHEN pa.status = 'Absent' THEN 1 ELSE 0 END) > 0
ORDER BY attendance_percentage ASC;
```
---
### Query 2: Tournament Revenue Analysis by Venue

**Description**: Analyzes tournament hosting, team participation, and revenue generation by venue.

**Justification**: Helps management evaluate venue profitability for tournaments. Identifies venues with better registration and payment completion rates to optimize future tournament planning.

```sql
SELECT v.venue_id,
    v.name AS venue_name,
    v.capacity,
    COUNT(DISTINCT t.tournament_id) AS total_tournaments,
    COUNT(tr.registration_id) AS total_team_registrations,
    SUM(t.entry_fee) AS total_potential_revenue,
    SUM(CASE WHEN tr.payment_status = 'Paid' THEN t.entry_fee ELSE 0 END) AS actual_revenue,
    ROUND((SUM(CASE WHEN tr.payment_status = 'Paid' THEN t.entry_fee ELSE 0 END) / SUM(t.entry_fee)) * 100, 2) AS payment_rate,
    (SELECT COUNT(*) FROM Tournaments WHERE venue_id = v.venue_id AND start_date > CURDATE()) AS upcoming_tournaments
FROM Venues v
JOIN Tournaments t ON v.venue_id = t.venue_id
JOIN Team_Registrations tr ON t.tournament_id = tr.tournament_id
GROUP BY v.venue_id, v.name, v.capacity
ORDER BY total_potential_revenue DESC;
```
---
### Query 3: The Most Active Teams Based on Tournament Participation

**Description**: Analyzes the performance and revenue generation of volleyball teams that participated in multiple tournaments during the year 2023. 

**Justification**: Provides valuable insights into which teams are the most active and financially impactful for the volleyball club. Teams that participate in more tournaments and pay higher entry fees contribute significantly to the club's revenue stream. By identifying these high-performing teams, the club can focus on maintaining strong relationships with them.

```sql
SELECT 
    t.team_id,
    t.team_name,
    COUNT(tr.tournament_id) AS total_tournaments_participated,
    SUM(tn.entry_fee) AS total_revenue_generated,
    AVG(tn.entry_fee) AS average_entry_fee
FROM Teams t
JOIN Team_Registrations tr ON t.team_id = tr.team_id
JOIN Tournaments tn ON tr.tournament_id = tn.tournament_id
WHERE 
    tn.start_date >= '2023-01-01' 
    AND tn.end_date <= '2023-12-31'
    AND tr.payment_status = 'Paid'
GROUP BY t.team_id, t.team_name
HAVING COUNT(tr.tournament_id) >= 2 AND AVG(tn.entry_fee) > 30
ORDER BY total_revenue_generated DESC;
```
**Result**: 
## Tournament Revenue Report
| team_id | team_name         | total_tournaments_participated | total_revenue_generated | average_entry_fee |
|---------|------------------|-------------------------------|------------------------|-------------------|
| 5       | Net Gainers      | 3                             | 205.00                 | 68.333333         |
| 2       | Bulldog Smash    | 2                             | 155.00                 | 77.500000         |
| 1       | Athens Aces      | 2                             | 80.00                  | 40.000000         |
| 3       | Athens Advantage | 2                             | 80.00                  | 40.000000         |

---
### Query 4: Breakdown of Players by Age Division 

**Description**: Categorizes players in the volleyball club into three age division (Juniors, High School, and Adult) based on their birth dates.

**Justification**: Helps the club understand the distribution of players across different age groups. Which can be used for planning age-specific programs, tournaments, and training sessions. The query can be run periodically to track changes in player demographics over time. 

```sql
SELECT 
    CASE
        WHEN TIMESTAMPDIFF(YEAR, p.birth_date, CURDATE()) < 14 THEN 'Juniors'
        WHEN TIMESTAMPDIFF(YEAR, p.birth_date, CURDATE()) BETWEEN 14 AND 18 THEN 'High School'
        ELSE 'Adult'
    END AS age_division,
    COUNT(p.player_id) AS player_count
FROM Players p
WHERE p.birth_date IS NOT NULL
    AND TIMESTAMPDIFF(YEAR, p.birth_date, CURDATE()) >= 10 -- Ensure players are at least 10 years old
GROUP BY age_division
HAVING COUNT(p.player_id) > 0 -- Ensure only divisions with players are included
ORDER BY player_count DESC;
```
**Result**:
### Age Divisions
| age_division | player_count |
|--------------|--------------|
| High School  | 8            |
| Adult        | 4            |

---

### Query 5: Tracking Player Details and Emergency Contacts


**Description:** Retrieves the player's name, the team they belong to, the name of their emergency contact, and the relationship of the contact to the player for players in the Athens Spring Open.

**Justification:** This query provides a clear overview of player details and their associated emergency contacts, which is essential for organizational and safety purposes. Having quick access to this information ensures that the club can respond effectively in case of emergencies and maintain accurate records for all participants. This enhances the overall safety and operational efficiency of the club.


```sql
SELECT 
       p.name AS player_name,
   pec.name AS emergency_contact_name,
    pec.phone AS emergency_contact_phone
FROM Player_Emergency_Contacts pec
JOIN Players p ON pec.player_id = p.player_id
JOIN Team_Registrations tr ON p.team_id = tr.team_id
JOIN Tournaments t ON tr.tournament_id = t.tournament_id
WHERE t.name = 'Athens Spring Open';

Result

| Player Name        | Emergency Contact Name | Emergency Contact Phone |
|--------------------|------------------------|-------------------------|
| Emma Davis         | John Davis             | 706-555-6001            |
| Emma Davis         | Laura Davis            | 706-555-6002            |
| Michael Chen       | Wei Chen               | 706-555-6003            |
| Sophia Rodriguez   | Carlos Rodriguez       | 706-555-6004            |
| Ethan Taylor       | Sarah Taylor           | 706-555-6007            |
| Isabella Martinez  | Miguel Martinez        | 706-555-6008            |
| Alexander Wright   | Jennifer Wright        | 706-555-6013            |
| Noah Garcia        | Elena Garcia           | 706-555-6011            |
| Mia Lee            | Daniel Lee             | 706-555-6012            |
```
---
### Query 6: Players and Teams in the 'Fall Championship'

**Description:** Analyzes which players, along with their team names and payment status, are registered in teams that have participated in the 'Fall Championship' tournament, and what their team's activity level and total entry fees paid.

**Justification:** This query provides valuable insights into player and team participation in the 'Fall Championship', including their financial contributions and activity levels. By identifying teams with high activity and payment compliance, the club can ensure financial stability and recognize high-performing teams. Additionally, understanding player engagement helps in fostering stronger relationships and improving overall tournament organization.


```sql
SELECT 
    p.name AS player_name,
    p.email AS player_email,
    t.team_name AS team_name,
    tr.payment_status AS team_payment_status,
    COUNT(DISTINCT tr.tournament_id) AS total_tournaments_participated,
    CASE 
        WHEN COUNT(DISTINCT tr.tournament_id) > 5 THEN 'Highly Active'
        WHEN COUNT(DISTINCT tr.tournament_id) BETWEEN 2 AND 5 THEN 'Moderately Active'
        ELSE 'Less Active'
    END AS team_activity_level,
    SUM(tn.entry_fee) AS total_entry_fees_paid
FROM Players p
JOIN Teams t ON p.team_id = t.team_id
JOIN Team_Registrations tr ON t.team_id = tr.team_id
JOIN Tournaments tn ON tr.tournament_id = tn.tournament_id
WHERE tn.name = 'Fall Championship'
GROUP BY p.name, p.email, t.team_name, tr.payment_status
HAVING COUNT(DISTINCT tr.tournament_id) >= 1  
   AND SUM(tn.entry_fee) > 0  
ORDER BY total_entry_fees_paid DESC;

Result

| Player Name        | Player Email       | Team Name           | Team Payment Status | Total Tournaments Participated | Team Activity Level | Total Entry Fees Paid |
|--------------------|--------------------|---------------------|---------------------|--------------------------------|---------------------|-----------------------|
| Alexander Wright   | alex.w@email.com   | Athens Advantage    | Pending             | 1                              | Less Active         | 60.00                 |
| Emma Davis         | emma.d@email.com   | Athens Aces         | Pending             | 1                              | Less Active         | 60.00                 |
| Ethan Taylor       | ethan.t@email.com  | Athens Advantage    | Pending             | 1                              | Less Active         | 60.00                 |
| Isabella Martinez  | isabella.m@email.com| Athens Advantage   | Pending             | 1                              | Less Active         | 60.00                 |
| Michael Chen       | michael.c@email.com| Athens Aces         | Pending             | 1                              | Less Active         | 60.00                 |
| Sophia Rodriguez   | sophia.r@email.com | Athens Aces         | Pending             | 1                              | Less Active         | 60.00                 |
```
---
### Query 7: Low Attendance Player Report

**Description:** This query retrieves a list of players who have attended 50% or fewer of their scheduled practice sessions. It includes the player’s name, their team, and their assigned coach, along with the total number of practices, attended practices, and their attendance percentage.

**Justification:** This query helps coaches and managers identify players with low attendance (≤50%) in practice sessions. Low attendance can indicate lack of engagement, scheduling conflicts, or potential dropouts. By tracking these players, coaches can address issues early, improve team performance, and optimize training resources. This ensures that teams remain competitive and that resources (like court time and coaching) are efficiently used.


```sql
SELECT 
    p.player_id,
    p.name AS player_name,
    t.team_name,
    c.name AS coach_name,
    COUNT(pa.attendance_id) AS total_practices,
    SUM(IF(pa.status = 'Present', 1, NULL)) AS practices_attended,
    ROUND((SUM(IF(pa.status = 'Present', 1, NULL)) / COUNT(pa.attendance_id)) * 100, 2) AS attendance_percentage
FROM Players p
JOIN Teams t ON p.team_id = t.team_id
JOIN Coaches c ON t.coach_id = c.coach_id
JOIN Player_Attendance pa ON p.player_id = pa.player_id
GROUP BY p.player_id, p.name, t.team_name, c.name
HAVING attendance_percentage <= 50
ORDER BY attendance_percentage ASC;
```

**Result**:
## Player Attendance Report  
| player_id | player_name       | team_name         | coach_name       | total_practices | practices_attended | attendance_percentage |  
|-----------|------------------|------------------|-----------------|----------------|--------------------|----------------------|  
| 3         | Sophia Rodriguez  | Athens Aces      | Maria Johnson   | 2              | 1                  | 50.00                |  
| 4         | Thomas Jackson    | Bulldog Smash    | Robert Smith    | 2              | 1                  | 50.00                |  
| 7         | Isabella Martinez | Athens Advantage | David Williams  | 2              | 1                  | 50.00                |  
| 11        | Mia Lee           | Net Gainers      | Sarah Brown     | 2              | 1                  | 50.00                |  

---

### Query 8: Active Players

**Description:** This query retrieves a list of players who have an active membership status by joining the Players and Player_Membership tables. It filters results to include only players where the membership status is marked as "Active."

**Justification:** This query helps club managers and administrators quickly identify currently enrolled members, ensuring that only eligible players participate in practices and tournaments. It also assists in membership tracking, financial management, and facility usage planning.

```sql
SELECT p.player_id, p.name, pm.status
FROM Players p
JOIN Player_Membership pm ON p.player_id = pm.player_id
WHERE pm.status = 'Active';
```

**Result**:
## Active Players Report  
| player_id | name               | status  |  
|-----------|-------------------|--------|  
| 1         | Emma Davis        | Active |  
| 2         | Michael Chen      | Active |  
| 3         | Sophia Rodriguez  | Active |  
| 4         | Thomas Jackson    | Active |  
| 5         | Olivia Kim        | Active |  
| 6         | Ethan Taylor      | Active |  
| 7         | Isabella Martinez | Active |  
| 8         | James Wilson      | Active |  
| 9         | Ava Thompson      | Active |  
| 10        | Noah Garcia       | Active |  
| 11        | Mia Lee           | Active |  
| 12        | Alexander Wright  | Active |  

---








[Add remaining queries in same format]


## Matrix of Query Features

| SQL Feature | Q1 | Q2 | Q3 | Q4 | Q5 | Q6 | Q7 | Q8 | Q9 | Q10 |
|-------------|----|----|----|----|----|----|----|----|----|----|
| Multiple table join | ✓ | ✓ | ✓ |  |  ✓  |   ✓ | ✓  | ✓   |    |    |
| Subquery |    | ✓ | ✓ |   |    |    |    |    |    |    |
| GROUP BY | ✓ | ✓ | ✓ | ✓ |    | ✓   |  ✓  |    |    |    |
| GROUP BY with HAVING | ✓ |    | ✓ | ✓ |    | ✓   | ✓   |    |    |    |
| Aggregate function | ✓ | ✓ | ✓ | ✓ |    |  ✓  |  ✓  |    |    |    |
| CASE statement | ✓ | ✓ |    | ✓ |    | ✓   |    |    |    |    |
| Multi-condition WHERE |    |    |    |    |  ✓  |   ✓ |    | ✓   |    |    |
| Built-in functions | ✓ | ✓ |    |    |    |    | ✓   |    |    |    |
| Calculated field | ✓ | ✓ | ✓ | ✓ |    |  ✓  | ✓   |    |    |    |
