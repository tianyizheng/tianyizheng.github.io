---
layout: page
title: Tianyi Zheng - About
description: The story of Tianyi Zheng, a master's student studying computer engineering at Georgia Tech
sitemap:
    priority: 0.7
    lastmod: 2018-09-09
    changefreq: weekly
---
## About Me

<span class="image left"><img src="{{ "/images/atlanta.jpg" | absolute_url }}" alt="" /></span>

I am currently pursuing a Master’s degree in Computer Engineering to prepare for a future career in software engineering. Prior to that, I graduated with the highest honor from Georgia Tech with a Bachelor’s degree in Mechanical Engineering with a concentration in robotics and automation.

I have a strong background in web and mobile application development. I enjoy programming and am proficient in python, C/C++, Java and Matlab. I love learning new skills and keeping up with the latest technology advances.

Here's some other things that I love: standup comedies, skiing, fantasy novels, fitness, italian food and the TV show Friends.

{% include skills.html %}

<br />

<h2 class="section-title"><i class="fa fa-graduation-cap"></i>Education</h2>

#### MS in Computer Engineering

#### Georgia Institute of Technology

<p>
  My major area of focus is in software engineering and networking. I also have a special interest in security.
</p>

#### BS in Mechanical Engineering

#### Georgia Institute of Technology

<p>
  I graduated with highest honor with a concentration in robotics and automation in May, 2017.
  In addition to my major area of study, I devoted my time in studying control theory, mechatronics and computer science. 
  I also conducted undergraduate research in creating control algorithm to BioMechanic instruments, worked as an intern for 
  a startup company prototyping a novel water-saving tap and served as philantrhopy liaison for Alpha Kappa Psi.
</p>

<h2 class="section-title"><i class="fa fa-briefcase"></i>Experiences</h2>
#### Software Developer Intern

<h4><a href="http://www.sap.com">SAP</a>, Atlanta</h4>

<p>
 I am part of SAP's Mobile Services on CloudFoundry team and have been the main contributor to a service that provides mobile application management for customers. I developed the web application following the microservice principle using Java and MongoDB on CloudFoundry. By collaborating across a global team and actively contributing to the service architecture meetings, I was able to create a solution to get around CloudFoundry's binding/authentication limitation for providing services to applications in different regions. 

 All developments were done in a test driven process. Technologies used included Java, Spring, Maven, MongoDb, CloudFoundry, Jenkins and Jira.

 Besides development, I also worked extensively on researching a way to deploy the new services to all SAP regions. It included deploying new service brokers, advertise the app as a service, and binding the centralized service to other regions’ applications.

 I also developed SOAPUI test automation scripts to perform smoke and integration tests for services. 

 Other responsibilities are Github repository management including merges, code reviews, approving pull requests, assisting QA teams to develop manual test execution processes and CloudFoundry team space management and monitoring
</p>

#### Lead Mechanical Intern

<h4><a href="http://www.mymuyu.com">Muyu Technology</a>, Beijing</h4>

<p>
 Muyu technology is a startup company developing novel water-saving tap systems.
  My main project was to design, develop and prototyp a vibration and noise reduction 
  solution for a small air pump in Solidworks while ensuring compactness, manufacturability 
  and improving its heat transfer efficiency. As the project lead, I worked with three other interns,
  delegated tasks, managed BOM and developed a lowcost bakelite module that secures the pump
  and reduces noise by 15 dB. Our module was later submitted for a patent application. I also worked closely with the electronics engineers, product designer 
  and the fluid engineer to optimize the size of the fluid module by 50% and ensure 
  correct dimensional requirements.
  <br> <br>
  I joined the company at its early stage and had the chance to work closely under the CTO. 
  In addition to engineering works, I also contacted potential parts suppliers and manufacturers 
  to survey and negotiate price. Most importantly I had the opportunity to experience the 
  product development cycle and pitch our product to angel inverstors and VC firms
</p>

#### Undergraduate Research Intern

<h4><a href="http://www.sulchek2.gatech.edu">Sulchek Lab</a>, Georgia Tech, Atlanta</h4>

<p>
    My reserach topic was on the rapid determination of microcantilevel
  quality factor to promote maintenance of the atomic force microscope tip-sample distance. 
  I developed automation scripts in Igor Pro to collect and filter thermal noise data with optimal 
  frequency for fast curve fitting using the Lorentzian function model. I further analyzed 
  the quality factor versus tip-sample distance behavior and designed a PID controller to achieve 
  fixed-distance tip-hovering that enables the AFM to collect live cell topology data autonomously
  over long periods of time.
</p>

<h2 class="section-title"><i class="fa fa-archive"></i> Class Projects</h2>

<dl>
	<dt><a href="https://github.com/AlexandrePalo/TrailBlazer">Trailblazer</a></dt>
	<dd>
		<p>A dynamic web application that recommends the most exciting tracks based on a single user location. Read more <a href="{{ "/blog/" | absolute_url }}">here</a>. Built with DjangoRestful, React, MongoDB and NetworkX.
</p>
	</dd>
	<dt><a href="https://github.gatech.edu/akeech3/MortalityPredictor">Fhirnet</a></dt>
	<dd>
		<p>A light weight SMART-on-Fhir web application designed for physicians for patient mortality prediction using neural net models. Built with Flask, SocketIO, SqlAlchemy, Postgres FHIR and Docker. Automated with Jenkins and deployed on GaTech HDAP. </p>
	</dd>
	<dt><a href="https://github.com/tianyizheng/Faceology">Faceology</a></dt>
	<dd>
		<p>An efficient one-stop networking solution iOS app with facial matching abilities to help users navigate networking events. Read more <a href="{{ "/blog/" | absolute_url }}">here</a>. Built with Swift, Flask and Postgres.</p>
	</dd>
  <dt><a href="https://github.com/tianyizheng/PVWebApp">Private Void</a></dt>
  <dd>
    <p>A movie recommendation web application for students based on their major and school standings. Developed CRUD operations for users, admins and movies. Built with Java EE, Hibernate NetBeans and Bootstrap.</p>
  </dd>
	<dt>The HugePole</dt>
	<dd>
		<p>Capstone design project where an intuitive lowcost no-ladder solution for Christmas lights hanging was designed and prototyped for the Homedepot company.</p>
	</dd>
	<dt>ChipSampler </dt>
	<dd>
		<p>An automatic Arduino system for collecting microfluidic chip samples that won the Best Design in Class award.</p>
	</dd>
</dl>

<h2 class="section-title"><i class="fa fa-archive"></i> Side Projects</h2>
<dl>
  <dt>This Site - Built with Jekyll and hosted on Github</dt>
  <dd>
    <p>I use this site not only as a profile hosting place, but also to wrtie my <a href="{{ "/blog/" | absolute_url }}">blog</a>. I mainly write about projects that I'm working on and what I learned from other exciting tech advances/resaerch papers.</p>
  </dd>
  <dt><a href="https://github.com/tianyizheng/wordCount">WikiPedia Vis(Developing)</a></dt>
  <dd>
    <p>I am aiming to create a site that helps people visualize relevant topics on a Wikipedia page. It started when I was just following a tutorial and grew bigger. Built with Flask, Redis, Angular, NLTK, Postgres and deployed on Heroku. </p>
  </dd>
  <dt><a href="https://github.com/tianyizheng/Solarize">Solarize - MindSumo</a></dt>
  <dd>
    <p>This was a failed attempt at participating on a MindSumo competition. However, I did learn a lot about ReactNative and created a functional UI.</p>
  </dd>
  <dt>Segue Bot - Hackathon</dt>
  <dd>
    <p>Designed and built a self balancing robot with Arduino. I mainly worked on sensor reading, pwm signal output and implementing a PID controller. </p>
  </dd>
</dl>
