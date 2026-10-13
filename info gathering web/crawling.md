## Web Crawling: Systematic Reconnaissance

Web crawling (or **spidering**) is the automated process of systematically browsing a website to map its structure and collect data. Unlike **fuzzing**, which relies on guessing potential paths, crawling follows the actual links present on the pages.

---

### 1. The Mechanics of a Web Crawler

A crawler functions as a bot that follows a recursive cycle to "discover" the web.

1. **Seed URL:** The starting point (e.g., `https://inlanefreight.com`).
    
2. **Fetching & Parsing:** The crawler downloads the page and extracts all links (`<a>` tags, scripts, etc.).
    
3. **Queueing:** Extracted links are added to a list of "to-be-visited" URLs.
    
4. **Iteration:** The process repeats until no new links are found or a depth limit is reached.
    

---

### 2. Crawling Strategies



| **Strategy**      | **Priority** | **Best Used For**                                                                |
| ----------------- | ------------ | -------------------------------------------------------------------------------- |
| **Breadth-First** | **Width**    | Getting a broad overview of the entire site structure and top-level directories. |
| **Depth-First**   | **Depth**    | Reaching deep into nested directories or following a specific path to its end.   |

### Breadth-First Crawling

![Flowchart showing a Seed URL leading to Page 1, which branches to Page 2 and Page 3. Page 2 connects to Page 4 and Page 5, while Page 3 connects to Page 6 and Page 7.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/144/ig_crawling_1.png)

`Breadth-first crawling` prioritizes exploring a website's width before going deep. It starts by crawling all the links on the seed page, then moves on to the links on those pages, and so on. This is useful for getting a broad overview of a website's structure and content.

### Depth-First Crawling

![Flowchart showing a Seed URL leading to Page 1, then to Page 2. Page 2 connects to Page 3, which branches to Page 4 and Page 5.](https://mermaid.ink/svg/pako:eNo9zz0PgjAQBuC_0twsg18LgwlfGyYG4uQ5VHoC0RZS2sEQ_rsnTezU98mlvXeGZlAEMbRWjp0oKzSTf4RQEylxrUo0gk9yu8iWxPaOhoxCk4goOok06I41XSELsGfIVsgDHBjyFYoAR4YivCEEGtiAJqtlr3iZ-fclgutIE0LMVyXtCwHNwnPSu6H-mAZiZz1twA6-7SB-yvfEyY9KOsp7ySX0X0n1brDn0HWtvHwB2SFOww)

In contrast, `depth-first crawling` prioritizes depth over breadth. It follows a single path of links as far as possible before backtracking and exploring other paths. This can be useful for finding specific content or reaching deep into a website's structure.




---

### 3. High-Value Data Extraction

Crawlers don't just find pages; they harvest specific data points that help build an attack profile:

- **Links (Internal/External):** Maps the site’s architecture and identifies third-party integrations or partner networks.
    
- **Comments:** HTML comments often contain "developer notes," internal usernames, or hints about the backend infrastructure (e.g., ``).
    
- **Metadata:** Page titles, descriptions, and author tags can reveal naming conventions or the software used to build the site.
    
- **Sensitive Files:** Bots can be tuned to look for specific patterns indicating exposure:
    
    - **Backups:** `.bak`, `.old`, `.zip`, `~` files.
        
    - **Config Files:** `web.config`, `settings.php`, `.env`.
        
    - **Logs:** `error_log`, `access_log`.
        

---

### 4. The Power of Contextual Analysis

Data collected in isolation is rarely useful. The true goal of crawling is **connecting the dots.**

> **Example of Correlation:**
> 
> 1. **Crawl Result:** You find a link to `/backup/`.
>     
> 2. **Metadata Result:** You find an author tag for "admin_jdoe".
>     
> 3. **Contextual Lead:** You check `/backup/jdoe.zip`. This logical jump identifies a critical exposure that a tool might miss but a human analyst notices through context.
>     

#### Key Observation: Directory Browsing

If a crawler identifies a pattern of links pointing to a specific directory (like `/files/` or `/uploads/`), it may reveal that **Directory Browsing** is enabled. This allows an attacker to see every file stored on the server, often bypassing security controls intended to hide sensitive data.

---

### 5. Crawling vs. Fuzzing Summary

| **Feature**          | **Crawling**                  | **Fuzzing**                                          |
| -------------------- | ----------------------------- | ---------------------------------------------------- |
| **Discovery Method** | Follows existing links.       | Guesses paths based on a wordlist.                   |
| **Visibility**       | Only finds what is linked.    | Can find unlinked/hidden files (e.g., `.php.bak`).   |
| **Usage**            | Mapping visible architecture. | Discovering hidden "Easter eggs" or forgotten files. |

