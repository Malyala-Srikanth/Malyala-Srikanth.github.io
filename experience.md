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
			<h1>VMock India Pvt Ltd</h1>
		</header>
		<!-- Content -->
		<p>Smart Career Development platform intended to offer CV feedback services by using machine learning, predictive analytics, and artificial intelligence techniques and compares students' CVs with those created by thousands of successful job hunters, enabling users across universities like CMU, Stanford, Chicago Booth, University of Oxford, Columbia, Kellogg, INSEAD to help write more effective resumes.</p>
		<h2 id="content">Data Scientist - II (April 2023 to Present)</h2>
		<dl>
			<dd>
				<ul>
					<li>Spearheaded a team of 3 people working on multiple projects including upgrading the version of SpaCy from 2.0.15 to 3.4.4 by retraining the text classification models, sub section detection using gradient boosting classifier replacing the current logics amounting to a decrease in the total API latency up to 50 percentage</li>
					<li>Finetuned LayoutLMForTokenClassification language model using transformers library to extract named entities in experience section which uses both text and spatial information from resume obtaining overall f1 score of 0.844.</li>
					<li>Researched on efficient single GPU training methodologies using PyTorch and presented it to the entire team of 12 data scientists, useful for faster and cost-effective finetunig of LLM’s.</li>
					<li>Extracted bullets with weak action verbs from resume and leveraged semantic search using  to suggest relevant strong action verbs with the help of OpenSearch (search engine).</li>
					<li>Developed and deployed a scalable API for providing potent action verb recommendations when weaker ones are used in bullet points using multi-qa-mpnet-base-dot-v1 contextual embeddings and OpenSearch as a vector database</li>
				</ul>
			</dd>
		</dl>
		<h2 id="content">Data Scientist - I (Dec 2020 to Mar 2023)</h2>
		<dl>
			<dd>
				<ul>
					<li>Researched and implemented ASPOSE, a third-party .NET-based tool, to provide support for DOCX Format for parsing resumes uploaded by students across 260+ universities and 10000+ benchmarks.</li>
					<li>Retrained Partially Labelled Latent Dirichlet Allocation (PLDA) to accurately predict the super-section of a chunk of resume based on n-grams of text and increased its top one accuracy from 81.9 to 89.6 percentage, top two accuracy from 92.2 to 96.7 percentage using k-fold cross validation.</li>
					<li>Finetuned bert-base-cased large language model (LLM) for merging bullets written in multiple lines, with classification head on top, utilizing DVC for efficient data tracking and MlFlow for metrics tracking and obtained an accuracy of around 93 percentage</li>
					<li>Built first version of the best-match system, which scores and ranks a given set of resumes against a job description by research and utilizing OpenSearch for searching and scoring based on entities and skills extracted from both the resume parser and JD parser</li>
					<li>Performed data extraction from multiple internal databases and the internet through web scraping, followed by thorough cleaning and normalization and extracted importance of skills using tf-idf on the basis of function</li>
				</ul>
			</dd>
		</dl>
	</div>
</section>
</div>
