---
author: "kaya"
date: 2024-08-29
title: "GISAID; Download Influenza virus genome sequence from GISAID database"
categories: ["code"]
tags: ["2024-08"]
---

# GISAID; Download Influenza virus genome sequence from GISAID database

##### 2024.10.19
##### *#code* *#genomics*
---

## Step 1. Download influenza genome

GISAID(https://gisaid.org/)에 로그인해서 Epiflu database에 접속한다.

![image](https://github.com/user-attachments/assets/453a0a34-2d17-446e-9b11-930d7e2eb5f1)

참고로 Epiflu database는 Epicov, EpiRSV와 같은 다른 db와 달리 접근 승인을 받아야 접속할수 있다.

접속했다면 Search & Browse에 들어가서 원하는 조건을 선택해준다.

![image](https://github.com/user-attachments/assets/0bb19330-2638-4cf4-adb6-7644f90f61f8)

이때
- Host를 human으로 설정하면 human에서 얻은 virus sequence들을 얻을수있다.
- Required Segment를 설정해주고 only complete로 설정해주면 8개 segment 데이터가 모두 존재하는 데이터셋의 sequence만 볼수있다.

선택했다면 search를 클릭해준다.

전체 체크박스를 클릭하고 download를 클릭하면 아래 창이 뜬다.

![image](https://github.com/user-attachments/assets/4587c5a4-aac1-4cf1-a8fe-8af8f35560c6)

- Format에서 Isolate as XLS (metadata only)를 선택하고 download하면, 선택한 sequence들의 metadata가 다운로드된다.
- Format에서 Sequence (DNA) as FASTA를 선택하고 DNA에서 all을 선택하면, 선택한 sequence들을 fasta 파일 형식으로 다운로드된다.

## Step 2. Sequence Preprocessing

다운로드한 fasta 파일을 열어보면 다음과 같이 구성되어있다.

```bash
     1 >A/HaNoi/BM870/2003|EPI_ISL_106499|2003-09-19|A/HaNoi/BM870/2003|EPI_ISL_106499|2003-09-19|HA|A/HaNoi/BM870/2003|EPI_ISL_106499|2003-09-19|HA|unknown|       unassigned
     2 aaataaaaacaaccaaaatgaaagtaaaactactggtcctgttatgtatatttacagctacatatgcagacacaatatgt
     3 ataggctaccatgccaacaactcaaccgacactgttgacacagtacttgagaagaatgtgacagtgacacactctgtcaa
     4 cctacttgaggacagtcacaatggaaaactatgtctactaaaaggaatagccccactacaattgggtaattgcagcgttg
     5 ccggatggatcttaggaaacccagaatgcgaattactgatttccaaggaatcatggtcctacattgtagaaacaccaaat
     6 cctgagaatggaacatgttacccaggttatttcgccgactatgaggaactgagggagcaattgagttcagtatcttcatt
     7 tgagaggttcgaaatattccccaaagaaagctcatggcccaaccacaccgtaaccggagtatcagcatcatgctcccata
     8 atgggaaaagcagtttttacagaaatttgctatggctgacggggaagagtggtttgtacccaaacctgagcaagtcctat
     9 gcaaacaacaaagagaaagaagtccttgtactatggggtgttcatcacccgcctaacataggggtccaaagggccctcta
    10 tcatacagaaaatgcttatgtctctgtagtgtcttcacattatagcagaagattcaccccagaaatagccaaaagaccca
    11 aagtaagagatcaggaaggaagaatcaactactactggactctgctggaacccggggatacaataatatttgaggcaaat
    12 ggaaatctaatagcgccaaggtatgctttcgcactgagtagaggctttggatcaggaatcatcacctcaaatgcaccaat
    13 ggatgaatgtgatgcgaagtgtcaaacacctcagggagctataaacagcagtcttcctttccagaatgtacacccagtca
    14 caataggagagtgtccaaagtatgtcaagagtgcaaaattaaggatggttacaggactaaggaacatcccatccattcaa
    15 tccaggggtttgtttggagccattgccggtttcattgaaggggggtggactggaatggtagatggttggtatggttatca
    16 tcatcagaatgagcaaggatctggctatgctgcagatcagaaaagtacacaaaatgccattaacgggattacaaacaagg
    17 tgaattctgtaattgagaaaatgaacactcaattcacagctgtgggcaaagaattcaacaaattggaaagaaggatggaa
    18 aacttaaataaaaaagttgatgatgggtttctagacatttggacatataatgcagaattgttggttctactggaaaatga
    19 aaggactttggatttccatgactccaatgtgaagaatctgtatgagaaagtaaaaagccaattaaagaataatgccaaag
    20 aaataggaaacgggtgttttgagttctatcacaagtgtaacgatgaatgcatggagagtgtgaaaaatggaacttatgac
    21 tatccaaaatattccgaagaatcaaagttaaacagggagaaaattgatggagtgaaattggaatcaatgggagtctatca
    22 gattctggcgatctactcaacagtcgccagttccctggttcttttggtctccctgggggcaatcagcttctggatgtgtt
    23 ccaatgggtctttgcagtgtagaatatgcatctaagaccagaatttcagaaatataaggaaaaa
    24 >A/HaNoi/BM870/2003|EPI_ISL_106499|2003-09-19|A/HaNoi/BM870/2003|EPI_ISL_106499|2003-09-19|MP|A/HaNoi/BM870/2003|EPI_ISL_106499|2003-09-19|MP|unknown|       unassigned
    25 atattgaaagatgagtcttctaaccgaggtcgaaacgtacgttctctctatcgtcccgtcaggccccctcaaagccgaga
    26 tcgcacagagacttgaagatgtatttgctggaaagaataccgatcttgaggctctcatggagtggctaaagacaagacca
    27 atcctgtcacctctgactaaggggattttaggatttgtgttcacgctcaccgtgcccagtgagcgaggactgcagcgtag
    28 acgctttgtccaaaatgcccttaatgggaatggggatccaaataatatggacagagcagtcaaactgtatcgaaagctta
    29 agagggagataacattccatggggccaaagaaatagcactcagttattctgctggtgcacttgccagttgtatgggactc
    30 atatacaacaggatgggggctgtgaccaccgaatcagcatttggccttatatgcgcaacctgtgaacagattgccgactc
    31 ccagcataagtctcataggcaaatggtaacaacaaccaatccattgataagacatgagaacagaatggttctggccagca
    32 ctacagctaaggctatggagcaaatggctggatcgagtgaacaagcagctgaggccatggaggttgctagtcaggccagg
    33 cagatggtgcaggcaatgagagccattgggactcatcctagctctagcactggtctgaaaaatgatctccttgaaaattt
    34 gcaggcctatcagaaacgaatgggggtgcagatgcaacgattcaagtgatcctcttgttgttgccgcaagtataattggg
    35 attgtgcacctgatattgtggattattgatcgccttttttccaaaagcatttatcgtatctttaaacacggtttaaaaag
    36 agggccttctacggaaggagtaccagagtctatgagggaagaatatcgagaggaacagcagaatgctgtggatgctgacg
    37 atggtcattttgtcagcatagagctagagtaaaaaacta
    ...
```

다운로드한 fasta 파일은 다음 경로에 위치한다고 할때,

```bash
$ pwd
/path/to/fasta/Raw

 $ ls
A-H1N1.fasta  A-H2N2.fasta  A-H5N1.fasta  A-H7N3.fasta  A-H7N9.fasta  B.fasta    
A-H1N2.fasta  A-H3N2.fasta  A-H5N6.fasta  A-H7N7.fasta  A-H9N2.fasta  
```
해당 데이터를 segment별로 분리하여 저장하는 전처리를 수행해줄것이다. 

```python
file_list = ['A-H1N1', 'A-H3N2', 'A-H5N1', 'B']

for file in file_list:
    # File path 
    cur_file = f"/path/to/fasta/Raw/{file}.fasta"

    # Open the fasta file
    with open(cur_file, 'r') as fasta_file:
        data = fasta_file.read().split('>')  # Split the file by '>' which marks new records

    # Process each entry
    for entry in data:
        if entry.strip():  # Ignore empty entries
            lines = entry.splitlines()
            cur_meta_temp = lines[0]  # First line is the meta data
            cur_meta = cur_meta_temp.split(' ')[0]
            cur_sequence = ''.join(lines[1:])  # Remaining lines form the sequence
            cur_length = len(cur_sequence)  # Length of the sequence

            # Extract cur_gene from the annotated dataframe based on cur_meta
            cur_gene_row = fasta_df_annotated[fasta_df_annotated['meta'] == cur_meta]
            if not cur_gene_row.empty:
                cur_gene = cur_gene_row.iloc[0]['gene']
                
                # Directory to save the processed file
                save_dir = f"/data3/projects/2020_MUTCLUST/Data/Projects/GISAID_revision/Influenza/Reference/Preprocessed/{cur_gene}"
                os.makedirs(save_dir, exist_ok=True)  # Create directory if it doesn't exist

                # Save each sequence to a new file
                save_file = os.path.join(save_dir, f"{file}.fasta")
                with open(save_file, 'a') as output_file:
                    output_file.write(f">{cur_meta_temp}\n{cur_sequence}\n")
```

전처리 수행 결과 fasta 파일들이 다음과 같이 정리되었다. 

```bash
$ pwd
/path/to/fasta/Preprocessed

$ ls
HA/  MP/  NA/  NP/  NS/  PA/  PB1/  PB2/

$ cd HA
$ ls
A-H1N1.fasta  A-H3N2.fasta  A-H5N1.fasta  B.fasta

```

## Step 3. Dynamic Sequence Aligning

각 sequence의 길이를 확인해보면, reference sequence와 유사하지만 완전히 일치하지 않는다. 이는 중복, 결실, 역위 등이 존재하기 때문이다.

따라서 해당 sequence들을 reference sequence와 길이를 맞춰줘야하는데, 이를 Dynamic Sequence Aligning이라고 한다. MAFFT를 사용해서 해당 작업을 수행할 수 있다.

먼저 각 subtype/segment의 reference sequence가 다음 형식으로 다음 경로에 존재한다고 할때

```bash
$ pwd
/path/to/fasta/Reference

$ ls
HA/  MP/  NA/  NP/  NS/  PA/  PB1/  PB2/

$ cd HA
$ ls
A-H1N1.fasta  A-H3N2.fasta  A-H5N1.fasta  B.fasta

$ view A-H1N1.fasta

1 >NC_026433.1 Influenza A virus (A/California/07/2009(H1N1)) segment 4 hemagglutinin (HA) gene, complete cds
2 ATGAAGGCAATACTAGTAGTTCTGCTATATACATTTGCAACCGCAAATGCAGACACATTATGTATAGGTTATCATGCGAACAATTCAACAGACACTGTAGACACAGTACTAGAAAAGAATGTAACAGTAACACACTCTGTTAACCTTCTAGAA    GACAAGCATAACGGGAAACTATGCAAACTAAGAGGGGTAGCCCCATTGCATTTGGGTAAATGTAACATTGCTGGCTGGATCCTGGGAAATCCAGAGTGTGAATCACTCTCCACAGCAAGCTCATGGTCCTACATTGTGGAAACACCTAGTTCA    GACAATGGAACGTGTTACCCAGGAGATTTCATCGATTATGAGGAGCTAAGAGAGCAATTGAGCTCAGTGTCATCATTTGAAAGGTTTGAGATATTCCCCAAGACAAGTTCATGGCCCAATCATGACTCGAACAAAGGTGTAACGGCAGCATGT    CCTCATGCTGGAGCAAAAAGCTTCTACAAAAATTTAATATGGCTAGTTAAAAAAGGAAATTCATACCCAAAGCTCAGCAAATCCTACATTAATGATAAAGGGAAAGAAGTCCTCGTGCTATGGGGCATTCACCATCCATCTACTAGTGCTGAC    CAACAAAGTCTCTATCAGAATGCAGATGCATATGTTTTTGTGGGGTCATCAAGATACAGCAAGAAGTTCAAGCCGGAAATAGCAATAAGACCCAAAGTGAGGGRTCRAGAAGGGAGAATGAACTATTACTGGACACTAGTAGAGCCGGGAGAC    AAAATAACATTCGAAGCAACTGGAAATCTAGTGGTACCGAGATATGCATTCGCAATGGAAAGAAATGCTGGATCTGGTATTATCATTTCAGATACACCAGTCCACGATTGCAATACAACTTGTCAAACACCCAAGGGTGCTATAAACACCAGC    CTCCCATTTCAGAATATACATCCGATCACAATTGGAAAATGTCCAAAATATGTAAAAAGCACAAAATTGAGACTGGCCACAGGATTGAGGAATATCCCGTCTATTCAATCTAGAGGCCTATTTGGGGCCATTGCCGGTTTCATTGAAGGGGGG    TGGACAGGGATGGTAGATGGATGGTACGGTTATCACCATCAAAATGAGCAGGGGTCAGGATATGCAGCCGACCTGAAGAGCACACAGAATGCCATTGACGAGATTACTAACAAAGTAAATTCTGTTATTGAAAAGATGAATACACAGTTCACA    GCAGTAGGTAAAGAGTTCAACCACCTGGAAAAAAGAATAGAGAATTTAAATAAAAAAGTTGATGATGGTTTCCTGGACATTTGGACTTACAATGCCGAACTGTTGGTTCTATTGGAAAATGAAAGAACTTTGGACTACCACGATTCAAATGTG    AAGAACTTATATGAAAAGGTAAGAAGCCAGCTAAAAAACAATGCCAAGGAAATTGGAAACGGCTGCTTTGAATTTTACCACAAATGCGATAACACGTGCATGGAAAGTGTCAAAAATGGGACTTATGACTACCCAAAATACTCAGAGGAAGCA    AAATTAAACAGAGAAGAAATAGATGGGGTAAAGCTGGAATCAACAAGGATTTACCAGATTTTGGCGATCTATTCAACTGTCGCCAGTTCATTGGTACTGGTAGTCTCCCTGGGGGCAATCAGTTTCTGGATGTGCTCTAATGGGTCTCTACAG
TGTAGAATATGTATTTAA
```

MAFFT를 사용해서 sequence aligning을 수행하는 코드는 다음과 같다. 터미널로 수행해줘도 되는데, run_mafft2.sh 파일을 생성해서 수행해줬다.

```bash
#!/bin/bash

# List of genes and files to process
gene_list=('NP')
file_list=('A-H1N1' 'A-H3N2' 'A-H5N1' 'B')

# Loop through each gene and file
for gene in "${gene_list[@]}"; do
    echo "Processing gene: $gene"
    
    for file in "${file_list[@]}"; do
        echo "Processing file: $file"
        
        # Define directories and file paths
        input_fasta="/path/to/fasta/Preprocessed/${gene}/${file}.fasta"
        reference_fasta="/path/to/fasta/Reference/${gene}/${file}.fasta"
        output_dir="/path/to/fasta/MAFFT/${gene}"
        output_fasta="${output_dir}/${file}.fasta"
        
        # Create output directory if it doesn't exist
        mkdir -p "$output_dir"
        
        # Perform multiple sequence alignment with MAFFT
        echo "Running MAFFT for ${gene}/${file}..."
        mafft --thread 50 --anysymbol --add "$input_fasta" --keeplength "$reference_fasta" > "$output_fasta"
        
        # Check if MAFFT was successful
        if [ $? -eq 0 ]; then
            echo "Alignment complete: $output_fasta"
        else
            echo "MAFFT alignment failed for ${gene}/${file}"
        fi
    done
done
```
```bash
$ ls
run_mafft2.sh

$ chmod +x run_mafft2.sh
$ ./run_mafft2.sh
```

수행 결과를 깔끔하게 확인하기 위해 데이터프레임 형태로 만들어주었다. 

```python
# 데이터 저장용 리스트
id_list = []
date_list = []
gene_list = []
subtype_list = []
region_list = []
sequence_list = []
length_list = []
file_list = []

subtypes = ['A-H1N1', 'A-H3N2', 'A-H5N1', 'B']  
genes = ['HA', 'PB2', 'PA', 'MP', 'NS', 'PB1', 'NA', 'NP']

for gene in genes:
    for subtype in subtypes:

        cur_file = f"/path/to/fasta/MAFFT/{gene}/{subtype}.fasta"
        cur_file_name = cur_file.split('/')[-1].split('.')[0]

        # 파일 읽기
        with open(cur_file, 'r') as f:
            lines = f.read().splitlines()

        cur_sequence = ""
        cur_meta = ""
        cur_id = ""
        cur_date = ""
        cur_gene = ""
        cur_subtype = ""
        cur_region = ""

        # FASTA 파일을 한 줄씩 처리
        for line in lines:
            if line.startswith('>'):
                # 이전 sequence 데이터를 처리
                if cur_meta:
                    #cur_sequence = cur_sequence.replace("-", "").replace("\n", "")
                    cur_length = len(cur_sequence)
                    id_list.append(cur_id)
                    date_list.append(cur_date)
                    gene_list.append(cur_gene)
                    subtype_list.append(cur_subtype)
                    region_list.append(cur_region)
                    sequence_list.append(cur_sequence)
                    length_list.append(cur_length)
                    file_list.append(cur_file_name)

                # 새로운 메타 데이터 처리
                cur_meta = line[1:]  # '>' 이후의 메타데이터
                cur_sequence = ""  # 새로운 시퀀스를 위한 초기화

                # NC로 시작하는 경우와 아닌 경우 처리
                if cur_meta.startswith("NC"):
                    #cur_id = '-'
                    cur_id = 'reference'
                    cur_date = '-'
                    cur_gene = gene
                    cur_subtype = '-'
                    cur_region = '-'
                else:
                    meta_split = cur_meta.split('|')
                    cur_id = meta_split[1] if len(meta_split) > 1 else '-'
                    cur_date = meta_split[2] if len(meta_split) > 2 else '-'
                    cur_gene = meta_split[6] if len(meta_split) > 6 else '-'

                    meta_slash_split = cur_meta.split('/')
                    cur_subtype = meta_slash_split[0] if len(meta_slash_split) > 0 else '-'
                    cur_region = meta_slash_split[1] if len(meta_slash_split) > 1 else '-'

            else:
                cur_sequence += line

        # 마지막 sequence 처리
        if cur_meta:
            cur_sequence = cur_sequence.replace("-", "").replace("\n", "")
            cur_length = len(cur_sequence)
            id_list.append(cur_id)
            date_list.append(cur_date)
            gene_list.append(cur_gene)
            subtype_list.append(cur_subtype)
            region_list.append(cur_region)
            sequence_list.append(cur_sequence)
            length_list.append(cur_length)
            file_list.append(cur_file_name)

# 데이터프레임 생성
fasta_df = pd.DataFrame({
    'id': id_list,
    'date': date_list,
    'gene': gene_list,
    'subtype': subtype_list,
    'region': region_list,
    'sequence': sequence_list,
    'length': length_list,
    'file': file_list
})
```

중복인 sequence를 제거하고 데이터를 확인해보자. subtype 'B'의 gene 'NA'에 해당하는 sequence의 length는 다음과 같이 맞춰진것을 확인할 수 있다.

```python
fasta_df_unique[(fasta_df_unique['gene'] == 'NA') & (fasta_df_unique['file'] == 'B')]
```
![image](https://github.com/user-attachments/assets/e91a8df9-8b46-4b70-92c2-dbf021bd21ad)



