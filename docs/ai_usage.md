# AI USE LOG


https://chatgpt.com/share/691ff1c9-da5c-8003-b524-431633bb1412
This is a link to the shared chat log


Prompt: How would I download UniProtKB data into a Google Colab without having to individually add each FASTA file? Could I use requests? 

Responses:
Yes — you can absolutely download UniProtKB FASTA files in Google Colab using requests (or wget/curl).
You do not need to manually upload/download each file. The key is to either:

Pull individual IDs programmatically, or

Use UniProt query URLs to download many sequences at once in a single FASTA file.

Below are the most useful patterns.

✅ Option 1 — Download multiple UniProt FASTA entries using a list of IDs

If you already have a list of UniProt accessions:

import requests

ids = ["Q9S7A8", "Q9ZP06", "P12345"]  # example UniProt IDs
url = "https://rest.uniprot.org/uniprotkb/stream?format=fasta&query="

query = " OR ".join(ids)
final_url = url + query

response = requests.get(final_url)

with open("sequences.fasta", "w") as f:
    f.write(response.text)

print("Downloaded sequences.fasta!")


This produces one FASTA file containing all sequences.

✅ Option 2 — Download all proteins from a UniProt query (recommended)

You can download FASTA by using any UniProt query, such as:

a species (e.g., Arabidopsis thaliana: organism_id:3702)

a protein family: family:"SWI/SNF"

a GO term: go:0005634

a gene name: gene:BRM

import requests

query = "organism_id:3702 AND gene:BRM"
url = f"https://rest.uniprot.org/uniprotkb/stream?format=fasta&query={query}"

response = requests.get(url)
response.raise_for_status()

with open("uniprot_results.fasta", "w") as f:
    f.write(response.text)

print("Finished!")


This is the cleanest way because UniProt handles the queries for you.

✅ Option 3 — Download the entire UniProt proteome
Example: Arabidopsis thaliana (UP000006548)
import requests

proteome = "UP000006548"
url = f"https://rest.uniprot.org/uniprotkb/stream?format=fasta&query=proteome:{proteome}"

r = requests.get(url)
r.raise_for_status()

with open("arabidopsis_proteome.fasta", "w") as f:
    f.write(r.text)

🧪 Option 4 — Batch download using mapping (ID ↔ Gene ↔ Accession)

If you have gene names and need UniProt IDs:

import requests

genes = ["BRM", "SWI3A", "SWI3B"]
query = " OR ".join([f"gene:{g}" for g in genes])

url = f"https://rest.uniprot.org/uniprotkb/stream?format=fasta&query={query}"
response = requests.get(url)

with open("complex_subunits.fasta", "w") as f:
    f.write(response.text)

📌 Notes
✔ Works in Colab
✔ No uploads required
✔ One file instead of many
✔ Fully automatable

If you want, I can help you:

write a script that downloads an entire list of IDs from a CSV,

download all subunits of a complex,

automatically generate FASTA files for every gene in a table you have.

Just tell me what your input looks like!

Prompt: Individual FASTA files

Response: Got it — you want to download each UniProt FASTA as an individual file, but automatically, without manually adding each one.
Yes, you can easily do this with requests in Google Colab.

Below is the cleanest template.

✅ Download individual FASTA files automatically using requests
Example: given a list of UniProt accessions
import requests
import os

# List of UniProt accessions you want to download
ids = ["Q9S7A8", "Q9ZP06", "P12345"]

# Create output folder
os.makedirs("fasta_files", exist_ok=True)

for uid in ids:
    url = f"https://rest.uniprot.org/uniprotkb/{uid}.fasta"
    r = requests.get(url)

    if r.status_code == 200:
        with open(f"fasta_files/{uid}.fasta", "w") as f:
            f.write(r.text)
        print(f"Downloaded {uid}")
    else:
        print(f"Failed to download {uid} (status {r.status_code})")

Result:

You get a folder:

fasta_files/
 ├── Q9S7A8.fasta
 ├── Q9ZP06.fasta
 ├── P12345.fasta


— one file per protein.

🔥 Want to pull an entire list automatically from a table?

If you have:

a CSV of UniProt IDs

gene names

AGI locus IDs

I can write the full automatic pipeline to:

read your file,

map IDs → UniProt accessions if needed,

download each FASTA into its own file.

Just tell me what your input looks like.
