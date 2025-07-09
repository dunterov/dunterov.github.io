---
layout: post
title: Generating confluence pages from AWS Tag Editor CSVs
date: 2025-07-09 07:00:01 +0100
categories: python aws confluence
---
<div style="text-align: center; background-color: #eee; padding: 10px;">
  <img src="{{ site.baseurl }}/images/aws-tags-to-confluence.png" alt="Tag editor" title="Tag editor" width="70%">
</div>
Some time ago, I was working on an AWS resource inventory and realized I needed a clean, structured way to present it in Confluence - especially since not everyone on the team had direct access to the AWS console.
Manually updating pages was time-consuming and error-prone, so I built a tool to automate that process.<br>
The [aws-csv-to-confluence](https://github.com/dunterov/aws-csv-to-confluence) transforms CSV files exported from the **AWS Tag Editor** into structured Confluence pages, with one page per service (like *EC2*, *S3*, *Lambda*, etc.). It's simple, small, and saved me a ton of time. Maybe it'll be useful for someone else too.

## What is that tool about?

The [aws-csv-to-confluence](https://github.com/dunterov/aws-csv-to-confluence) is a simple CLI utility that converts AWS CSV exports (as said, from AWS Tag Editor) into a structured set of Confluence pages - one per service. It's written in Python, packaged via [Poetry](https://python-poetry.org/), and optional Dockerfile.

It reads your exported CSV, groups resources by service, and creates or updates corresponding pages in Confluence using the [Atlassian Python API](https://github.com/atlassian-api/atlassian-python-api).

## Where and how to get CSV file?

To export all your AWS resources, start by logging in to the AWS Management Console. Then, navigate to the **Tag Editor**, as shown below.

<div style="text-align: center; background-color: #eee; padding: 10px;">
  <img src="{{ site.baseurl }}/images/tag_editor_1.png" alt="Tag editor" title="Tag editor" width="90%">
</div>

In the Tag Editor:

* Select **All regions**
* Choose **All supported resource types**
* Click **Search resources**

<div style="text-align: center; background-color: #eee; padding: 10px;">
  <img src="{{ site.baseurl }}/images/tag_editor_2.png" alt="Tag editor" title="Tag editor" width="90%">
</div>

After a few moments (depending on the size of your project), all resources will be listed. Download the file - this will be the source input for the tool.

*(Tip: You can filter resources however you like - by region, specific tags, or anything else available in the AWS Tag Editor. The tool will generate Confluence pages from any valid CSV you provide.)*

## What do I get as a result?

As a result, this tool will create a number of Confluence pages under provided `--parent` page.

<div style="text-align: center; background-color: #eee; padding: 10px;">
  <img src="{{ site.baseurl }}/images/confl_pages.png" alt="Example pages" title="Example pages" width="300">
</div>

Each page contains a simple Confluence table listing key attributes like identifier, type, region, and ARN.

<div style="text-align: center; background-color: #eee; padding: 10px;">
  <img src="https://github.com/dunterov/aws-csv-to-confluence/raw/main/page_example.png" alt="Example page" title="Example page" width="90%">
</div>

Since this is Confluence, you can easily sort the table by any column using the table headers.

## Quick Start

Before you begin, make sure you have:

* An [**Atlassian API token**](https://github.com/dunterov/aws-csv-to-confluence/tree/main?tab=readme-ov-file#how-to-create-an-atlassian-api-token)
* The **parent Confluence page ID**  
  *(Tip: You can find it in the page URL - look for the `pageId=` parameter.)*

### Option 1: run locally with Poetry

Clone the repository and install dependencies using [Poetry](https://python-poetry.org/):

```bash
git clone https://github.com/dunterov/aws-csv-to-confluence.git
cd aws-csv-to-confluence
poetry install
```

Then run the script:

```bash
poetry run aws-csv-to-confluence \
    --user alice@example.com \
    --token <ATLASSIAN_TOKEN> \
    --url https://mycorp.atlassian.net/wiki \
    --parent 123456789 \
    --file ./resources.csv \
    --subtitle prod \
    --clean
```

### Option 2: use Docker

If you'd rather not install Poetry, you can build and run the tool with Docker:

```bash
git clone https://github.com/dunterov/aws-csv-to-confluence.git
cd aws-csv-to-confluence

docker build -t aws-csv-to-confluence .

docker run --rm -v `pwd`/resources.csv:/resources.csv aws-csv-to-confluence \
    --user alice@example.com \
    --token <ATLASSIAN_TOKEN> \
    --url https://mycorp.atlassian.net/wiki \
    --parent 123456789 \
    --file /resources.csv \
    --clean
```

For more details, check out the [README](https://github.com/dunterov/aws-csv-to-confluence/blob/main/README.md).

## Final Thoughts

This tool is a classic "scratch-my-own-itch" project. It saved me time - and hopefully, it'll do the same for you or your team.  
In my setup, I've built some automation around it, so every week I get a fresh, clean AWS inventory right inside Confluence.
If you're managing AWS resources and need a clear, structured view in Confluence, give it a try.
The code is open source under the MIT license. Feedback, issues, and contributions are always welcome.

Thanks for reading! If this tool helps you - or if you have suggestions to make it better - feel free to open an issue or submit a pull request.  

See you!
