---
layout: page
title: Experience
description: 
image: assets/images/experience.jpeg
nav-menu: true
---

<!-- Main -->
<div id="main" class="alt">
<!-- One -->
<section id="one">
	<div class="inner">
		<header class="major">
			<h1>VMock Pvt Ltd</h1>
		</header>
		<!-- Content -->
		<h2 id="content">Data Scientist (Dec 2020 to Present)</h2>
		<dl>
			<dt><b>Resume Parser</b></dt>
			<dd>
				<ul>
					<li>Researched and implemented ASPOSE, a third-party .NET-based tool, to provide support for the DOCX format.</li>
					<li>Retrained a topic model that accurately predicts the super-section (such as education or work experience) of a chunk of resume based on its text and increased its top one accuracy from 81.9 to 89.6 percentage, top two accuracy from 92.2 to 96.7 percentage using k-fold cross validation.</li>
					<li>Finetuned a BERT-based model for merging bullets written in multiple lines with classification head on top, utilizing DVC for efficient data tracking and MLflow for metrics tracking and obtained an accuracy of around 93 percent</li>
					<li>Developed and deployed a scalable API for providing potent action verb recommendations when weaker ones are used in bullet points using MPNet embeddings and OpenSearch</li>
				</ul>
			</dd>
			<dt><b>Best Match (ATS)</b></dt>
			<dd>
				<ul>
					<li>Created the initial version of a best-match algorithm, capable of scoring and ranking a set of resumes against a job description (JD) to determine the most suitable candidates.</li>
					<li>Performed data extraction from multiple internal databases and the internet through web scraping, followed by thorough cleaning and normalization.</li>
					<li>Conducted extensive research and successfully utilized OpenSearch to search and score resumes based on relevant entities and skills, obtained through advanced resume and job description parsing techniques.</li> 
					<li>Designed and implemented a scalable API using Flask and Celery, and deployed it on a robust infrastructure leveraging Docker, Amazon Web Services (AWS) EC2 Container Service (ECS), and AWS Fargate for optimal performance and reliability.</li>
				</ul>
			</dd>
			<dt><b>JD Parser</b></dt>
			<dd>
				<ul>
				<li>Conducted research and optimized GECToR (grammatical error correction) model to achieve a 50 percent reduction in latency, processing 20 sentences in 2 seconds instead of 4 seconds. Additionally, modified the model output to effectively handle edge cases through postprocessing.</li>
				<li>Utilized Docker, AWS Sagemaker and AWS Deep Learning Containers to redeploy the optimized model</li>
				</ul>
			</dd>
		</dl>
	</div>
</section>
</div>
