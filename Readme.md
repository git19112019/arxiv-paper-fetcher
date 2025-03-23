# Arxiv Paper Fetcher

A repository focused on fetching and displaying research papers from ArXiv using their API.

## Features

- Fetches research papers from ArXiv based on a keyword.
- Displays the title, authors, abstract preview, and PDF link of the papers.
- Sorts results by the most recent submissions.

## Installation

To install and set up this project, follow these steps:

1. Clone the repository:
    ```bash
    git clone https://github.com/git19112019/arxiv-paper-fetcher.git
    ```

2. Navigate to the project directory:
    ```bash
    cd arxiv-paper-fetcher
    ```

3. Ensure you have Python installed (preferably Python 3).

## Usage

To use this project, follow these instructions:

1. Run the Python script `arxiv.py`:
    ```bash
    python arxiv.py
    ```

2. When prompted, enter the search keyword and the number of results you want to display.

3. The script will query the ArXiv API and display the titles, authors, abstract previews, and PDF links of the research papers.

## Dependencies

This project requires the following dependencies:
- Python standard libraries: `urllib` and `xml.etree.ElementTree` (no external dependencies required).

## Framework

This project uses a compiler framework named `ArxivPaperFetcher` to integrate the idea, pseudo-code, and Python code. Below is the YAML configuration for the framework:

````yaml
compiler_framework:
  - idea: |
      Create a program that fetches research papers from the ArXiv API based on a search keyword and displays the results.
  - pseudo_code: |
      1. Define constants for the ArXiv API base URL.
      2. Define a function `fetch_arxiv_papers` that:
         - Takes a keyword and number of results as arguments.
         - Constructs the query URL with the given keyword and sorting by the latest submission date.
         - Sends the request to the ArXiv API.
         - Parses the response data.
         - Handles possible errors.
      3. Define a function `parse_arxiv_results` that:
         - Takes the XML response data as input.
         - Parses the XML to extract relevant information about each paper (title, authors, summary, PDF link).
         - Displays the extracted information.
      4. Define a `main` function that:
         - Prompts the user for a search keyword and number of results.
         - Calls `fetch_arxiv_papers` with the user inputs.
      5. Ensure the `main` function is called when the script is run.
  - python_code: |
      import urllib.error
      import urllib.parse
      import urllib.request
      import xml.etree.ElementTree as ET

      # Constants: URL cơ bản cho ArXiv API
      API_URL = "http://export.arxiv.org/api/query?"

      def fetch_arxiv_papers(keyword, num_results=5):
          """
          Tìm kiếm bài báo trên ArXiv sử dụng API với sắp xếp theo thời gian gần nhất.
          Args:
              keyword (str): Từ khóa tìm kiếm trên ArXiv
              num_results (int): Số lượng kết quả tối đa muốn nhận
          """
          # Xây dựng URL API với sắp xếp theo thời gian
          query_params = {
              'search_query': f"all:{keyword}",
              'start': 0,
              'max_results': num_results,
              'sortBy': 'submittedDate',  # Sắp xếp theo ngày tháng
              'sortOrder': 'descending'  # Mới nhất trước
          }
          query_url = API_URL + urllib.parse.urlencode(query_params)
          print(f"Querying ArXiv API with URL: {query_url}")

          try:
              # Gửi yêu cầu tới API
              response = urllib.request.urlopen(query_url)
              data = response.read().decode('utf-8')
              parse_arxiv_results(data)
          except urllib.error.HTTPError as e:
              print(f"HTTP Error {e.code}: {e.reason}")
          except urllib.error.URLError as e:
              print(f"URL Error: {e.reason}")
          except Exception as e:
              print(f"Unexpected error: {e}")

      def parse_arxiv_results(data):
          """
          Phân tích dữ liệu XML trả về từ API của ArXiv và hiển thị kết quả.
          Args:
              data (str): Dữ liệu XML trả về từ ArXiv API
          """
          # Phân tích cú pháp XML
          root = ET.fromstring(data)
          namespace = {'atom': 'http://www.w3.org/2005/Atom'}

          # Tìm tất cả các bài báo (entry)
          entries = root.findall('atom:entry', namespace)
          if not entries:
              print("No results found.")
              return

          for entry in entries:
              try:
                  # Trích xuất thông tin bài báo
                  title = entry.find('atom:title', namespace).text.strip()
                  authors = [author.find('atom:name', namespace).text for author in entry.findall('atom:author', namespace)]
                  summary = entry.find('atom:summary', namespace).text.strip()
                  pdf_link = entry.find('atom:id', namespace).text.strip()

                  # Hiển thị thông tin bài báo
                  print(f"Title: {title}")
                  print(f"Authors: {', '.join(authors)}")
                  print(f"Abstract (Preview): {summary[:300]}...")  # Giới hạn tóm tắt 300 ký tự
                  print(f"PDF Link: {pdf_link}")
                  print("=" * 50)
              except AttributeError:
                  print("Error parsing a paper. Skipping...")
                  continue

      def main():
          """
          Điểm bắt đầu của chương trình. Nhận tham số dòng lệnh, thực hiện tìm kiếm, và hiển thị kết quả.
          """
          keyword = input("Enter the search keyword: ")  # Nhập từ khóa tìm kiếm
          num_results = int(input("Enter the number of results to display: "))  # Số lượng kết quả tối đa

          # Gửi yêu cầu và xử lý kết quả
          fetch_arxiv_papers(keyword, num_results)

      if __name__ == '__main__':
          main()
