# Project-_desk-
Creating the desk using the python dictionaries ,functions , and libraries 
import json
import os
import re
from datetime import datetime

DATA_FILE = "helpdesk_data.json"

# Default Knowledge Base across college inquiry domains
DEFAULT_KNOWLEDGE_BASE = {
    "Admissions": [
        {
            "id": "ADM-01",
            "question": "What are the eligibility criteria for undergraduate B.Tech programs?",
            "answer": "Candidates must have completed 10+2 with Physics, Mathematics, and Chemistry/Computer Science with at least 60% aggregate marks and a valid entrance test score.",
            "keywords": ["eligibility", "btech", "criteria", "ug", "undergraduate", "marks", "cutoff"]
        },
        {
            "id": "ADM-02",
            "question": "What is the application deadline and how do I apply?",
            "answer": "Applications close on June 30th annually. You can submit your application online through the college admission portal at admissions.college.edu.",
            "keywords": ["deadline", "apply", "application", "portal", "date", "form"]
        },
        {
            "id": "ADM-03",
            "question": "What documents are required during physical verification?",
            "answer": "Original 10th and 12th marksheets, transfer certificate, migration certificate, entrance admit/rank card, government photo ID, and 4 passport-size photographs.",
            "keywords": ["documents", "verification", "certificate", "id", "photos"]
        }
    ],
    "Fees & Scholarship": [
        {
            "id": "FEE-01",
            "question": "How can I pay my semester tuition fees?",
            "answer": "Fees can be paid online via the Student ERP Portal using Net Banking, UPI, or Credit/Debit Card. Demand drafts in favor of 'The Registrar' are also accepted at the accounts desk.",
            "keywords": ["fees", "tuition", "payment", "pay", "online", "net banking", "dd"]
        },
        {
            "id": "FEE-02",
            "question": "Are merit-based and need-based scholarships available?",
            "answer": "Yes, merit scholarships are awarded to students in the top 5% of their class. Need-based tuition waivers are available for families with annual income below INR 2.5 LPA.",
            "keywords": ["scholarship", "merit", "waiver", "financial aid", "need", "concession"]
        }
    ],
    "Examination": [
        {
            "id": "EXM-01",
            "question": "What is the minimum attendance requirement to appear for end-semester exams?",
            "answer": "A minimum of 75% attendance in each registered theory and lab course is mandatory to appear for the end-semester examinations.",
            "keywords": ["attendance", "exam", "hall ticket", "eligibility", "75%"]
        },
        {
            "id": "EXM-02",
            "question": "How do I apply for re-evaluation or photocopy of answer scripts?",
            "answer": "Submit the re-evaluation form on the student portal within 10 days of grade declaration along with a nominal processing fee of INR 500 per course.",
            "keywords": ["re-evaluation", "rechecking", "marks", "grade", "answer script", "paper"]
        }
    ],
    "Hostel & Accommodation": [
        {
            "id": "HST-01",
            "question": "How is hostel accommodation allocated to first-year students?",
            "answer": "Hostel rooms are allotted on a first-come, first-served basis based on distance from home and admission rank. Allocation starts after seat confirmation.",
            "keywords": ["hostel", "room", "accommodation", "allotment", "stay", "mess"]
        },
        {
            "id": "HST-02",
            "question": "What are the hostel curfew and gate timings?",
            "answer": "The main campus gates close at 10:00 PM for hostel residents. Prior written permission from the Warden is required for late entry or night-outs.",
            "keywords": ["curfew", "timings", "gate", "night out", "rules", "warden"]
        }
    ],
    "Library": [
        {
            "id": "LIB-01",
            "question": "How many books can a student borrow and for what duration?",
            "answer": "Undergraduate students can borrow up to 4 books for 14 days. Postgraduate students can borrow up to 6 books for 28 days. Renewals can be done online once.",
            "keywords": ["library", "books", "borrow", "issue", "limit", "renewal", "return"]
        },
        {
            "id": "LIB-02",
            "question": "How do I access digital library resources and IEEE/ScienceDirect journals off-campus?",
            "answer": "Login using your institutional Google Workspace ID via the OpenAthens / RemoteXs portal (library.college.edu/remote).",
            "keywords": ["digital library", "ieee", "journals", "online library", "remote", "books"]
        }
    ],
    "Placement & Training": [
        {
            "id": "TPO-01",
            "question": "What are the eligibility criteria to register for campus placements?",
            "answer": "Students must have a CGPA of 6.5 or above with no active backlogs at the start of the 7th semester to register with the Training & Placement Cell (T&P).",
            "keywords": ["placement", "tpo", "jobs", "internship", "cgpa", "criteria", "backlog", "recruit"]
        },
        {
            "id": "TPO-02",
            "question": "Does the college provide pre-placement training programs?",
            "answer": "Yes, mandatory aptitude, coding, mock interviews, and communication training are organized throughout the 5th and 6th semesters.",
            "keywords": ["training", "aptitude", "mock interview", "soft skills", "preparation"]
        }
    ]
}


def load_data():
    """Loads database from JSON or initializes with defaults."""
    if os.path.exists(DATA_FILE):
        try:
            with open(DATA_FILE, "r", encoding="utf-8") as f:
                return json.load(f)
        except Exception:
            pass

    # Initial data structure
    initial_data = {
        "knowledge_base": DEFAULT_KNOWLEDGE_BASE,
        "tickets": [],
        "ticket_counter": 1000
    }
    save_data(initial_data)
    return initial_data


def save_data(data):
    """Saves data back to the JSON store."""
    try:
        with open(DATA_FILE, "w", encoding="utf-8") as f:
            json.dump(data, f, indent=4)
    except IOError as e:
        print(f"\n[!] Error saving data: {e}")


def display_header(title):
    """Prints a styled section header."""
    print("\n" + "=" * 65)
    print(f" {title.center(63)} ")
    print("=" * 65)


def browse_by_domain(data):
    """Allows user to browse FAQs categorized by college domain."""
    kb = data["knowledge_base"]
    domains = list(kb.keys())

    while True:
        display_header("BROWSE BY COLLEGE DOMAIN")
        for idx, domain in enumerate(domains, start=1):
            count = len(kb[domain])
            print(f"  [{idx}] {domain} ({count} FAQs)")
        print("  [0] Return to Main Menu")

        choice = input("\nSelect a domain [0-{}]: ".format(len(domains))).strip()
        if choice == "0":
            break
        if not choice.isdigit() or not (1 <= int(choice) <= len(domains)):
            print("[!] Invalid selection. Please enter a valid number.")
            continue

        selected_domain = domains[int(choice) - 1]
        faqs = kb[selected_domain]

        display_header(f"{selected_domain.upper()} FAQs")
        for idx, faq in enumerate(faqs, start=1):
            print(f"\nQ{idx}: {faq['question']}")
            print(f"Ans: {faq['answer']}")

        input("\nPress Enter to return to domain list...")


def search_faqs(data):
    """Performs keyword and token overlap search across all FAQs."""
    display_header("SMART INQUIRY SEARCH")
    query = input("Enter your question or keywords (e.g., 'hostel curfew', 'library books'): ").strip()
    if not query:
        print("[!] Search query cannot be empty.")
        return

    # Normalize and tokenize input query
    clean_query = re.sub(r"[^\w\s]", " ", query.lower())
    query_tokens = set(clean_query.split())

    matches = []
    for domain, faqs in data["knowledge_base"].items():
        for faq in faqs:
            score = 0
            # Check keyword matches
            for kw in faq.get("keywords", []):
                for token in query_tokens:
                    if token in kw or kw in token:
                        score += 3

            # Check question text matches
            q_clean = re.sub(r"[^\w\s]", " ", faq["question"].lower()).split()
            for token in query_tokens:
                if token in q_clean:
                    score += 2

            # Check answer text matches
            a_clean = re.sub(r"[^\w\s]", " ", faq["answer"].lower()).split()
            for token in query_tokens:
                if token in a_clean:
                    score += 1

            if score > 0:
                matches.append((score, domain, faq))

    # Sort matches by relevance score descending
    matches.sort(key=lambda x: x[0], reverse=True)

    if not matches:
        print("\n[i] No direct answers found matching your query.")
        print("    Would you like to submit a formal inquiry ticket to the Help Desk?")
        create_now = input("    Raise a ticket now? (y/n): ").strip().lower()
        if create_now == "y":
            submit_ticket(data, prefill_query=query)
        return

    print(f"\nFound {len(matches)} relevant result(s):\n")
    for rank, (score, domain, faq) in enumerate(matches[:4], start=1):
        print(f"--- Result #{rank} [{domain}] (Relevance Score: {score}) ---")
        print(f"Q: {faq['question']}")
        print(f"A: {faq['answer']}\n")

    input("Press Enter to return...")


def submit_ticket(data, prefill_query=""):
    """Submits a student inquiry ticket."""
    display_header("SUBMIT HELP DESK TICKET")
    print("If your query wasn't answered in the FAQs, our staff will review your ticket.")

    student_name = input("Your Full Name: ").strip()
    while not student_name:
        print("[!] Name is required.")
        student_name = input("Your Full Name: ").strip()

    student_id = input("Student ID / Roll No (or 'Guest'): ").strip() or "Guest"

    domains = list(data["knowledge_base"].keys()) + ["General Inquiry / Other"]
    print("\nSelect Department:")
    for idx, domain in enumerate(domains, start=1):
        print(f"  [{idx}] {domain}")

    dept_choice = input(f"Choose department [1-{len(domains)}]: ").strip()
    if dept_choice.isdigit() and 1 <= int(dept_choice) <= len(domains):
        department = domains[int(dept_choice) - 1]
    else:
        department = "General Inquiry / Other"

    subject = input("Subject / Brief Topic: ").strip()
    while not subject:
        print("[!] Subject is required.")
        subject = input("Subject / Brief Topic: ").strip()

    if prefill_query:
        print(f"Initial inquiry captured: '{prefill_query}'")
        use_prefill = input("Use this as detailed description? (y/n): ").strip().lower()
        if use_prefill == "y":
            description = prefill_query
        else:
            description = input("Detailed Inquiry Description: ").strip()
    else:
        description = input("Detailed Inquiry Description: ").strip()

    while not description:
        print("[!] Description cannot be empty.")
        description = input("Detailed Inquiry Description: ").strip()

    # Generate ticket ID and timestamp
    data["ticket_counter"] += 1
    ticket_id = f"TICK-{data['ticket_counter']}"
    timestamp = datetime.now().strftime("%Y-%m-%d %H:%M:%S")

    new_ticket = {
        "ticket_id": ticket_id,
        "student_name": student_name,
        "student_id": student_id,
        "department": department,
        "subject": subject,
        "description": description,
        "status": "Open",
        "created_at": timestamp,
        "resolution_notes": ""
    }

    data["tickets"].append(new_ticket)
    save_data(data)

    print("\n" + "-" * 50)
    print(" [✓] TICKET SUBMITTED SUCCESSFULLY!")
    print(f" Ticket ID    : {ticket_id}")
    print(f" Department   : {department}")
    print(f" Status       : Open")
    print(f" Submission   : {timestamp}")
    print("-" * 50)
    print("Please keep your Ticket ID to track updates.\n")
    input("Press Enter to continue...")


def track_ticket(data):
    """Tracks status of an existing ticket by ID."""
    display_header("TRACK TICKET STATUS")
    ticket_id = input("Enter Ticket ID (e.g., TICK-1001): ").strip().upper()

    ticket = next((t for t in data["tickets"] if t["ticket_id"] == ticket_id), None)
    if not ticket:
        print(f"\n[!] No ticket found with ID: {ticket_id}")
        input("Press Enter to return...")
        return

    print("\n" + "=" * 50)
    print(f" Ticket Details : {ticket['ticket_id']}")
    print("=" * 50)
    print(f" Student Name   : {ticket['student_name']} ({ticket['student_id']})")
    print(f" Department     : {ticket['department']}")
    print(f" Date Created   : {ticket['created_at']}")
    print(f" Status         : {ticket['status']}")
    print(f" Subject        : {ticket['subject']}")
    print(f" Description    : {ticket['description']}")
    if ticket.get("resolution_notes"):
        print(f" Staff Remarks  : {ticket['resolution_notes']}")
    else:
        print(" Staff Remarks  : Pending review by department officer.")
    print("=" * 50)
    input("\nPress Enter to return...")


def admin_portal(data):
    """Administrative portal for staff to view and resolve tickets."""
    display_header("STAFF / ADMIN PORTAL")
    pin = input("Enter Staff Access PIN (default: admin123): ").strip()
    if pin != "admin123":
        print("[!] Authentication failed. Incorrect PIN.")
        return

    while True:
        display_header("STAFF DASHBOARD")
        total = len(data["tickets"])
        open_count = sum(1 for t in data["tickets"] if t["status"] == "Open")
        in_prog_count = sum(1 for t in data["tickets"] if t["status"] == "In Progress")
        resolved_count = sum(1 for t in data["tickets"] if t["status"] == "Resolved")

        print(f" Total Tickets: {total} | Open: {open_count} | In Progress: {in_prog_count} | Resolved: {resolved_count}\n")
        print("  [1] View All Tickets")
        print("  [2] View Tickets by Status (Open / In Progress / Resolved)")
        print("  [3] Update / Resolve a Ticket")
        print("  [4] Add New FAQ to Knowledge Base")
        print("  [0] Log Out to Main Menu")

        choice = input("\nSelect administrative action [0-4]: ").strip()
        if choice == "0":
            break
        elif choice == "1":
            display_tickets_table(data["tickets"])
        elif choice == "2":
            status_filter = input("Enter status to filter by (Open/In Progress/Resolved): ").strip().title()
            filtered = [t for t in data["tickets"] if t["status"].lower() == status_filter.lower()]
            display_tickets_table(filtered, title=f"Tickets with Status: {status_filter}")
        elif choice == "3":
            update_ticket_flow(data)
        elif choice == "4":
            add_faq_flow(data)
        else:
            print("[!] Invalid option.")


def display_tickets_table(tickets, title="ALL SUBMITTED TICKETS"):
    """Prints a structured table of tickets."""
    display_header(title)
    if not tickets:
        print("  No tickets found.")
        input("\nPress Enter to return...")
        return

    header_fmt = "{:<10} {:<15} {:<15} {:<12} {:<18}"
    print(header_fmt.format("ID", "STUDENT", "DEPARTMENT", "STATUS", "DATE"))
    print("-" * 72)
    for t in tickets:
        print(header_fmt.format(
            t["ticket_id"],
            t["student_name"][:14],
            t["department"][:14],
            t["status"],
            t["created_at"][:16]
        ))
    print("-" * 72)
    input("\nPress Enter to continue...")


def update_ticket_flow(data):
    """Updates a ticket's status and remarks."""
    ticket_id = input("\nEnter Ticket ID to update: ").strip().upper()
    ticket = next((t for t in data["tickets"] if t["ticket_id"] == ticket_id), None)
    if not ticket:
        print(f"[!] Ticket {ticket_id} not found.")
        return

    print(f"\nCurrent Status: {ticket['status']}")
    print(f"Subject: {ticket['subject']}")
    print(f"Description: {ticket['description']}")

    print("\nSelect New Status:")
    print("  [1] Open")
    print("  [2] In Progress")
    print("  [3] Resolved")
    st_choice = input("Choice [1-3] (Press Enter to keep current): ").strip()
    status_map = {"1": "Open", "2": "In Progress", "3": "Resolved"}
    if st_choice in status_map:
        ticket["status"] = status_map[st_choice]

    remarks = input("Add Resolution Note / Admin Response: ").strip()
    if remarks:
        ticket["resolution_notes"] = remarks

    save_data(data)
    print(f"[✓] Ticket {ticket_id} updated successfully!")


def add_faq_flow(data):
    """Allows staff to dynamically append new FAQs to any domain."""
    kb = data["knowledge_base"]
    domains = list(kb.keys())

    display_header("ADD NEW FAQ")
    print("Select domain:")
    for idx, d in enumerate(domains, start=1):
        print(f"  [{idx}] {d}")

    d_choice = input(f"Choice [1-{len(domains)}]: ").strip()
    if not d_choice.isdigit() or not (1 <= int(d_choice) <= len(domains)):
        print("[!] Invalid domain selection.")
        return

    domain = domains[int(d_choice) - 1]
    question = input("Enter Question: ").strip()
    answer = input("Enter Answer: ").strip()
    keywords_raw = input("Enter comma-separated keywords (e.g., fee, due date, portal): ").strip()

    keywords = [k.strip().lower() for k in keywords_raw.split(",") if k.strip()]
    new_faq_id = f"{domain[:3].upper()}-{len(kb[domain]) + 1:02d}"

    kb[domain].append({
        "id": new_faq_id,
        "question": question,
        "answer": answer,
        "keywords": keywords
    })

    save_data(data)
    print(f"[✓] New FAQ '{new_faq_id}' added under {domain}!")


def main():
    """Main application loop."""
    data = load_data()

    while True:
        display_header("CAMPUS CENTRAL: INTEGRATED COLLEGE HELP DESK")
        print("  [1] Browse FAQs by Department / Domain")
        print("  [2] Search Knowledge Base (Ask a Question)")
        print("  [3] Submit an Inquiry / Help Desk Ticket")
        print("  [4] Track Existing Ticket Status")
        print("  [5] Staff / Administrative Portal")
        print("  [0] Exit System")

        choice = input("\nEnter your choice [0-5]: ").strip()

        if choice == "1":
            browse_by_domain(data)
        elif choice == "2":
            search_faqs(data)
        elif choice == "3":
            submit_ticket(data)
        elif choice == "4":
            track_ticket(data)
        elif choice == "5":
            admin_portal(data)
        elif choice == "0":
            print("\nThank you for using Campus Central Help Desk. Goodbye!")
            break
        else:
            print("[!] Invalid selection. Please enter a number between 0 and 5.")

if __name__ == "__main__":
    main()

