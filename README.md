# Retrieval-Augmented Generation (RAG) Supported Methodology for Evaluating Management Efforts Contributed to Promoting Low Carbon Practice

本项目通过 RAGFlow API 实现知识库的创建、管理和查询，并能够自动对文档内容进行评分。

This project implements knowledge base creation, management, and querying via the RAGFlow API, and provides automated scoring for document content.

## 功能特点 / Features

* **配置管理**: 从 `config.yaml` 加载配置信息
    * **Configuration**: Load configuration from `config.yaml`.
* **客户端初始化**: 初始化 RAGFlow 客户端
    * **Client Initialization**: Initialize the RAGFlow client.
* **数据集管理**: 创建/管理 RAGFlow 数据集（知识库）
    * **Dataset Management**: Create and manage RAGFlow datasets (Knowledge Bases).
* **文档上传**: 上传本地文档到数据集
    * **Document Upload**: Upload local documents to the dataset.
* **文档解析**: 解析上传的文档
    * **Parsing**: Parse uploaded documents.
* **自动评分**: 根据提示词对文档内容进行评分
    * **Auto-Scoring**: Score document content based on provided prompts.
* **结果保存**: 将评分结果和引用数据保存到 Excel 文件
    * **Result Saving**: Save scoring results and citation data to Excel files.
* **批量处理**: 支持批量处理多个城市和评分变量
    * **Batch Processing**: Support batch processing for multiple cities and scoring variables.

## 项目结构 / Project Structure

```text
/
├── config.yaml             # 配置文件 / Configuration file
├── requirements.txt        # Python依赖 / Python dependencies
├── README.md               # 本说明文件 / This documentation
├── main.py                 # 主执行脚本（单次测试） / Main execution script (Single run)
├── batch_run.py            # 批量处理脚本 / Batch processing script
├── 得分变量提示词.xlsx      # 评分变量和提示词配置 / Scoring variables and prompt config
├── docs/                   # 文档目录（按城市分类） / Documents directory (Categorized by city)
│   ├── 北京/               # 北京市相关文档 / Documents for Beijing
│   ├── 上海/               # 上海市相关文档 / Documents for Shanghai
│   └── ...                 # 其他城市文档 / Documents for other cities
└── src/                    # 源代码目录 / Source code directory
    ├── __init__.py         # 标记'src'为Python包 / Marks 'src' as a Python package
    ├── config_loader.py    # 配置加载逻辑 / Configuration loading logic
    ├── ragflow_client.py   # RAGFlow客户端初始化 / RAGFlow client initialization
    ├── ragflow_ops.py      # RAGFlow数据集和文档操作 / RAGFlow dataset & document operations
    ├── utils.py            # 工具函数（JSON解析、Excel保存等） / Utilities (JSON parsing, Excel saving, etc.)
    └── QAandLog.py         # 问答和日志记录功能 / Q&A and logging functions
```

## 使用方法 / Usage

### 环境准备 / Environment Setup

1.  **创建 Python 虚拟环境 / Create Python Virtual Environment**
    ```bash
    conda create -n ragflow-env python=3.10
    conda activate ragflow-env
    ```

2.  **安装依赖 / Install Dependencies**
    ```bash
    pip install -r requirements.txt
    ```

### 配置修改 / Configuration

1.  **修改 config.yaml 中的 RAGFlow 服务器地址 / Update RAGFlow Server Address**

    将 `config.yaml` 中的 `ragflow.base_url` 修改为服务器 IP 地址：
    Update `ragflow.base_url` in `config.yaml` to your server's IP address:

    ```yaml
    ragflow:
      api_key: "ragflow-E3NzM3YWI2MGQ1ZTExZjA4MmM2MDI0Mm"
      base_url: "[http://10.61.77.156:80](http://10.61.77.156:80)" # 修改为服务器IP地址 / Change to server IP
    ```

### 单次测试 / Single Run Test

1.  **修改 config.yaml 中的文档路径和查询 / Update Document Path and Queries**
    ```yaml
    document_upload:
      folder_path: "./docs/北京"  # 指定要上传的文档目录 / Specify document directory
    
    qa_example:
      score_variable: "非化石能源发展和应用的规划目标"  # 指定得分变量 / Specify score variable
      queries:  # 指定查询提示词 / Specify query prompts
        - "文件中如果有阐述...获得20分。"
    ```

2.  **运行 main.py 进行测试 / Run main.py**
    ```bash
    python main.py
    ```

3.  **查看结果 / View Results**

    运行结束后，结果将保存在 `outputs/运行记录.xlsx` 文件中，包含两个 sheet：
    After completion, results are saved in `outputs/运行记录.xlsx` with two sheets:

    * `结果_得分变量名`: 包含得分结果 / Contains scoring results.
    * `引用数据`: 包含引用的文档块 / Contains reference document chunks.

    两个 sheet 中的数据通过 `timestamp` 时间戳列进行关联，方便手动查看每个得分点对应的引用块是否合理。相同时间戳的记录表示它们来自同一次查询处理。
    Data in both sheets is linked via the `timestamp` column, facilitating manual verification of reference chunks for each score. Records with the same timestamp belong to the same query process.

### 批量测试 / Batch Processing

1.  **准备文档 / Prepare Documents**

    在 `docs` 目录下创建城市子目录（如 `北京`、`上海` 等），并将相应的文档放入对应目录。
    Create city subdirectories (e.g., `Beijing`, `Shanghai`) under `docs` and place relevant documents inside.

2.  **准备评分变量和提示词 / Prepare Scoring Variables and Prompts**

    编辑 `得分变量提示词.xlsx` 文件，包含两列：
    Edit `得分变量提示词.xlsx`, containing two columns:
    * `score_variable`: 得分变量名称 / Name of the scoring variable.
    * `queries`: 对应的查询提示词 / Corresponding query prompts.

3.  **运行批量处理脚本 / Run Batch Script**
    ```bash
    python batch_run.py
    ```

4.  **批处理工作原理 / How Batch Processing Works**

    `batch_run.py` 会执行以下操作 / `batch_run.py` will:
    * 自动扫描 `docs` 目录下的所有子文件夹 / Automatically scan all subfolders in `docs`.
    * 读取 `得分变量提示词.xlsx` 中的所有评分变量和提示词 / Read all variables and prompts from the Excel file.
    * 为每个城市和评分变量组合生成配置 / Generate configuration for each City-Variable combination.
    * 依次修改 `config.yaml` 文件中的关键配置并运行 `main.py` / Sequentially modify `config.yaml` and run `main.py`.
    * 所有结果将保存在同一个 Excel 文件中，可通过时间戳列区分不同批次的结果 / All results are saved in a single Excel file, distinguished by timestamps.

## 注意事项 / Notes

1.  确保 RAGFlow 服务器已启动并可访问。
    Ensure the RAGFlow server is running and accessible.
2.  Excel 文件中的提示词格式需要正确，包含得分点、概念和分值信息。
    Ensure the prompt format in the Excel file is correct, including scoring points, concepts, and values.
3.  批量处理可能需要较长时间，请耐心等待。
    Batch processing may take a long time; please be patient.
4.  如遇到 JSON 解析错误，可能是提示词格式有问题，请检查并修正。
    If JSON parsing errors occur, check and correct the prompt format.
5.  所有结果都会保存在 `outputs/运行记录.xlsx` 文件中，可以按时间戳区分不同批次的结果。
    All results are saved in `outputs/运行记录.xlsx`; use timestamps to distinguish between batches.

## 未来待增加功能 / Future Features

1.  **文件元数据更新 / File Metadata Update**
    * 根据文件的时间信息为知识库文件更新元数据。
        Update knowledge base file metadata based on file timestamps.
    * 参考 RAGFlow 官方讨论 / Reference RAGFlow discussion: [GitHub Issue #6603](https://github.com/infiniflow/ragflow/issues/6603)
    * 这将允许按时间过滤文档，更好地管理文档版本。
        This will allow filtering documents by time for better version management.
