---
date : 2025-08-01
tags: ['2025-08']
categories: ['알고리즘']
bookHidden: true
title: "MutClust 코드 리펙토링 #3 utils"
---

# MutClust 코드 리펙토링 #3 utils

#2025-08-01

---

MutClust 알고리즘의 코드 구성은 아래와 같은데

```plain text
MutClust
├── sc/
│    └── lib.py
│    └── arg_parser.py
│    └── utils.py // 전처리 및 분석
└── Test
```

utils.py는 데이터 전처리 및 분석 함수를 포함한다.

```python
# === Fasta 전처리 ===
def fasta2csv(home_dir, nation_dir, filechunk, ref, outdir):
    for file in filechunk:
        path = os.path.join(home_dir, nation_dir, file)
        filename = os.path.splitext(os.path.basename(file))[0]
        outpath = os.path.join(outdir, f"{filename}.csv")
        if not os.path.exists(outpath):
            df = DataFrame({'ref': ref.values, 'pos': ref.index})
            seq = ''.join(open(path).readlines()[1:]).strip()
            df['mut'] = [a if a != ref[i] else '' for i, a in enumerate(seq)]
            df.to_csv(outpath, index=False)

def gisaid_fasta2csv(homedir=f"{GISAID_DIR}/Sequence/Preprocessed/"):
    inputdir = os.path.join(homedir, 'MSA_fasta')
    outdir = os.path.join(homedir, 'MSA_mutationinfo')
    Path(outdir).mkdir(exist_ok=True, parents=True)
    core_n = 100
    args_list = []
    for nation_dir in get_dirnames_list(inputdir):
        filelist = get_filenames_list(os.path.join(inputdir, nation_dir))
        for chunk in array_split(filelist, core_n):
            args_list.append((inputdir, nation_dir, chunk, ref_seq, outdir))
    with Pool(core_n) as pool:
        pool.map(fasta2csv, args_list)

# === Nucleotide 전처리 ===
def get_nucleotide_sequence_dict(seq_dir):
    seq_dict = dict()
    seq_list = get_filenames_list(seq_dir)
    for file in seq_list:
        filepath = os.path.join(seq_dir, file)
        df = read_csv(filepath, index_col=0)
        df.name = file.split('.')[0]
        df = df.reset_index(drop=True)
        seq_dict[df.name] = df
    return seq_dict

def getNucleotideRefSeqbyGene():
    return read_csv('/data3/projects/2020_MUTCLUST/Data/Annotation/Nucleotide/covid_annotation.tsv', sep=' ')

def make_nucleotide_mutclust_input(outdir, name, seq_dict=None):
    if not os.path.exists(outdir):
        print(outdir + ' is not exist')
        return

    output_path = os.path.join(outdir, name + '_mutclust_input.tsv')
    freq_df_ATGC_path = os.path.join(outdir, name + '_freq_ATGC.csv') 

    pos_list, freq_list, per_list, entropy_list = [], [], [], []

    if not os.path.exists(freq_df_ATGC_path):
        if seq_dict is None:
            print('load seq_dict')
            return 
        freq_df = DataFrame.from_dict(seq_dict).transpose().fillna(0).astype(int)
        freq_df = freq_df.sort_index()
        freq_df = freq_df[list(IUPAC_CODES.keys())][['A','T','G','C']]
        freq_df.to_csv(freq_df_ATGC_path)
    else:
        freq_df = read_csv(freq_df_ATGC_path, index_col=0)

    for pos in freq_df.index:
        freq = freq_df.loc[pos]
        cnt_n = freq.sum()
        percentage = freq / cnt_n
        entrpy = entropy(percentage, base=2)

        percentage.drop(ref_seq[pos], inplace=True)
        freq.drop(ref_seq[pos], inplace=True)

        pos_list.append(int(pos))
        freq_list.append(freq.sum())
        per_list.append(percentage.sum())
        entropy_list.append(entrpy)

    mutclust_input_df = DataFrame({
        'Position': pos_list,
        'Frequency': freq_list,
        'Percentages': per_list,
        'Entropy': entropy_list
    })
    mutclust_input_df.to_csv(output_path, sep='\t', index=False)
    return mutclust_input_df

# === Mutation 데이터 병렬 처리 ===
def read_thead(filepathlist, return_list, i):
    ref_seq_sr = getNucleotideRefSeq()
    sub_dict = {pos: Counter({k: 0 for k in IUPAC_CODES.keys()}) for pos in ref_seq_sr.index}

    for filepath in filepathlist:
        df = read_csv(filepath, index_col=0).fillna('').reset_index(drop=True)
        for index, mut in enumerate(df['mut']):
            symbol = mut if mut else ref_seq_sr[index + 1]
            if symbol in sub_dict[index + 1]:
                sub_dict[index + 1][symbol] += 1
            else:
                sub_dict[index + 1][symbol] = 1

    return_list.append(sub_dict)
    print(f"{i}th process complete!")

def merge_thread(poslist, sub_dict_list, return_dict):
    for pos in poslist:
        count_dict = sum([d[pos] for d in sub_dict_list], Counter())
        merged_dict = {k: count_dict.get(k, 0) for k in IUPAC_CODES.keys()}
        return_dict[pos] = merged_dict

def load_mutationinfo(input_dir=COVID19_MUTATIONINFO_DIR, sample_list=None):
    core_n, split_n = 100, 1000 
    sub_dict_list = Manager().list()
    filelist = get_file_paths_recursive(input_dir)
    
    if sample_list:
        filelist = [f for f in filelist if os.path.basename(f).split('.')[0] in sample_list]
    print(f'sample_n: {len(sample_list)}')

    splited_filepaths = array_split(filelist, split_n)
    parameter_list = [(chunk, sub_dict_list, i) for i, chunk in enumerate(splited_filepaths)]
    print('read thread start!')
    multi_processing(read_thead, parameter_list, core_n=core_n)
    print('read thread end!')

    merged_dict = Manager().dict()
    poslist = ref_seq.index
    splited_poslist = array_split(poslist, split_n)
    sub_dict_list = list(sub_dict_list)
    parameter_list = [(pos_chunk, sub_dict_list, merged_dict) for pos_chunk in splited_poslist]

    print('merge thread start!')
    multi_processing(merge_thread, parameter_list, core_n=core_n)
    print('merge thread end!')
    return dict(merged_dict)

# === Matrix 생성 병렬 처리 ===
def make_matrix_thread(file_list):
    clusters_df = pd.read_csv(os.path.join(GISAID_MUTCLUST_OUTPUT_DIR, 'clusters_hscore.txt'), sep='\t')
    column_list = [f"c{i}({row['left_position']},{row['right_position']})" for i, row in clusters_df.iterrows()]
    cluster_df = pd.DataFrame(columns=column_list)

    for path in file_list:
        df = pd.read_csv(path)
        patient_name = os.path.splitext(os.path.basename(path))[0]
        cluster_df.loc[patient_name] = 0
        for pos in df[df['mut'].notnull()]['pos']:
            cluster_idx = clusters_df[(clusters_df['left_position'] <= pos) & (pos <= clusters_df['right_position'])].index
            cluster_df.loc[patient_name][cluster_idx] += 1
    return cluster_df

def make_matrix(mutationinfo_dir, out_dir, tag, cpu_n=60):
    print('starting make matrix!')
    pool = Pool(processes=cpu_n)
    file_list = get_file_paths_recursive(mutationinfo_dir)
    results = pool.map(make_matrix_thread, array_split(file_list, cpu_n))
    pd.concat(results).to_csv(os.path.join(out_dir, f'cluster_matrix_{tag}.csv'))
    pool.close()
    pool.join()

# === H-score 계산 ===
def add_HSCORE():
    df = pd.read_csv(os.path.join(MUTCLUST_INPUT_DIR, 'gisaid_mutclust_input.tsv'), sep='\t')
    df[HSCORE] = df[PER] * df[ENT]
    df.to_csv(os.path.join(MUTCLUST_INPUT_DIR, 'gisaid_mutclust_input_with_score.tsv'), sep='\t', index=False)

# === 주석(Annotation) ===
def annotation():
    import ast
    mapping_df = pd.read_csv(os.path.join(GISAID_METADATA_DIR, 'merged_info.tsv'), sep='\t', index_col=0)
    for i, row in mapping_df.iterrows():
        mapping_df.loc[i] = [ast.literal_eval(val) for val in row]
    print(mapping_df)

def make_clade_divide_mutation():
    clade_dir = './clade_divide_mutation'
    start_dict = getStartDict()
    for file in get_filenames_list(clade_dir):
        df = read_csv(os.path.join(clade_dir, file), sep='\t')
        print(df)

# === 병렬 처리 유틸리티 ===
def multi_processing(func, parameter_list, core_n=100):
    proc, proc_excution, proc_end = [], [], []
    for param in parameter_list:
        proc.append(Process(target=func, args=param))

    while proc or proc_excution:
        for _ in range(len(proc)):
            if len(proc_excution) < core_n:
                p = proc.pop(0)
                p.start()
                proc_excution.append(p)
            else:
                break
        for p in proc_excution[:]:
            if not p.is_alive():
                proc_excution.remove(p)
                p.join()
                p.close()
                proc_end.append(p)

# === 메인 실행 ===
if __name__ == '__main__':
    annotation()

```
