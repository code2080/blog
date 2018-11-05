---
title: "My curriculum vitae"
date: 2018-09-27T00:00:00+07:00
draft: false
hide: true
---

<div>
  <style>
    .article-entry h1 {
      font-size: 1.5em;
      margin-top: 50px;
      margin-bottom: 15px;
    }
    h2 {
      font-weight: normal !important;
      font-size: 100% !important;
    }
    .progress {
      margin-top: -20px;
      margin-bottom: -20px;
      width: 100%;
      max-width: 360px;
      height: 10px;
      border-radius: 2px;
      background-color: #e2e2e2;
    }
    .progress:after {
      content: "";
      display: block;
      height: 100%;
      border-top-left-radius: 2px;
      border-bottom-left-radius: 2px;
      background-color: #404040;
    }
    .reactnative:after {
      width: 80%;
    }
    .reactjs:after {
      width: 70%;
    }
    .django:after {
      width: 65%;
    }
    .go:after {
      width: 20%;
      /* animation: go 1.2s linear infinite; */
    }
    .reading:after {
      width: 80%;
    }
    .writing:after {
      width: 65%;
    }
    .listening:after {
      width: 60%;
    }
    .speaking:after {
      width: 35%;
    }
    .lds-dual-ring {
      display: inline-block;
      width: 26px;
      height: 26px;
    }
    .lds-dual-ring:after {
      content: " ";
      display: block;
      width: 26px;
      height: 26px;
      margin: 1px;
      border-radius: 50%;
      border: 5px solid #e2e2e2;
      border-color: #e2e2e2 transparent #e2e2e2 transparent;
      animation: lds-dual-ring 1.2s linear infinite;
    }
    @keyframes lds-dual-ring {
      0% {
        transform: rotate(0deg);
      }
      100% {
        transform: rotate(360deg);
      }
    }
    @keyframes go {
      0% {
        width: 20%;
      }
      50% {
        width: 22%;
      }
      100% {
        width: 20%;
      }
    }
    .hide {
      display: none;
    }
    .description {
      margin: -5px 20px 30px;
      font-size: 0.85em;
      line-height: 1.4em;
      color: gray;
    }
    .intro {
      padding: 20px;
      background-color: #23241F;
      color: #C5C8C6;
      border-radius: 2px;
      font-size: 14px;
      font-family: "Source Code Pro", Consolas, Monaco, Menlo, Consolas, monospace, '华文中宋';
    }
  </style>
  <script>
    function showLoading() {
      document.getElementById("trap").childNodes[1].classList.remove("hide");
      document.getElementById("trap").childNodes[3].classList.add("hide");
    }
    function showError() {
      document.getElementById("trap").childNodes[1].classList.add("hide");
      document.getElementById("trap").childNodes[3].classList.remove("hide");
    }
    setTimeout(showError, 1000);
    function refresh() {
      showLoading();
      console.log("This is just the fun thing that I made in my CV. I graduated from Electric Power University of Electronics - Telecommunication. Thanks for your time.");
      setTimeout(showError, 1000);
    }
  </script>
</div>


# Introduction
---

HIEU KIEU|<span style="font-weight: 400">Full Stack Developer</span>
---|---
Birth|05 February 1989
From|Hanoi, Vietnam
Email|hieuktr@gmail.com

<div class="intro">
I am highly focused on creating performance and clean application. I am very interested in joining a good team to create the significant products make good affect to many people, make change to the world. In the future, I am looking to expand my skills in enterprise design pattern, microservice architecture, high performance app, machine learning, teamwork and leadership.
</div>

# Education
---

<div id="trap">
  <div class="lds-dual-ring"></div>
  <div class="hide" style="line-height: 30px">
    500 Internal server error - <a onclick="refresh()" style="cursor: pointer">Refresh</a>
  </div>
</div>

# Work experience
---

## VNPost

<div class="description">
<i>Oct 2012 - Present ~ <span id="time_vnpost"><span></i><br/>
Working in Research and development department as a Web developer, Mobile developer, Reseacher building many products on our business.
</div>

## Code4Startup

<div class="description">
<i>Dec 2015 - Present ~ <span id="time_c4s"><span></i><br/>
This is a startup in Australia, I am working remotely as a Content maker building programming courses for our students, also a Web developer building code4startup.com and other products.
</div>

## Sosub Team

<div class="description">
<i>Feb 2016 - Present ~ <span id="time_sosub"><span></i><br/>
As a remote full-stack developer, also a technical leader works with about 20 interesting people to create an non-profit organization bringing useful knowledge from the world to Vietnam.
</div>

## S-Team

<div class="description">
<i>Apr 2018 - Present ~ <span id="time_s_team"><span></i><br/>
Begins with outsourcing a system helping the government collecting the martyr's tomb information. Then  we research about some education platforms such as Khan Academy, OpenEDX. Now we are researching for apply gamification to education.
</div>

## MainJS Team

<div class="description">
<i>May 2018 - Aug 2018 ~ <span id="time_mainjs"><span></i><br/>
As an React Native Developer working with 5 people to build an extreme application template.
</div>

<div>
  <script>
    function monthDiff(d1, d2=new Date()) {
      var months;
      months = (d2.getFullYear() - d1.getFullYear()) * 12;
      months -= d1.getMonth();
      months += d2.getMonth();
      var y = parseInt(months/12);
      var m = months%12;
      var result = "";
      if (y > 0) result += y === 1 ? y + " year" : y + " years";
      if (m > 0) result += " " + (m === 1 ? m + " month" : m + " months");
      return result;
    }
    document.getElementById("time_vnpost").innerHTML = monthDiff(new Date(2012, 9, 0));
    document.getElementById("time_c4s").innerHTML = monthDiff(new Date(2015, 11, 0));
    document.getElementById("time_sosub").innerHTML = monthDiff(new Date(2016, 1, 0));
    document.getElementById("time_s_team").innerHTML = monthDiff(new Date(2018, 3, 0));
    document.getElementById("time_mainjs").innerHTML = monthDiff(new Date(2018, 4, 0), new Date(2018, 7, 0));
  </script>
</div>

# Projects
---

## Sosub.org [⤴](https://sosub.org)

<div class="description">
An education platform that helping people get more knowledge by watching the videos with two subtitles supported English and Vietnamese.
</div>

## Programming courses [⤴](https://code4startup.com)

<div class="description">
There are many courses that I have made at Code4Startup such as UberEats Clone (in Python, Android), Fiverr Clone (in Django), ProductHunt Clone (in React.js), Airbnb Clone (in React Native), Bet Dapp with Ethereum (in Django, Solidity), Facebook Chatbot (in Django)... helping my company raise the hundreds of thoudsands of dollars on Kickstarter in two years.
</div>

## Delivery for postman [⤴](https://play.google.com/store/apps/details?id=com.baophat)

<div class="description">
The mobile app for delivering that I have built with React Native. Currently, it is using by the thoudsands of people in VNPost.
</div>

## Gomdon [⤴](https://play.google.com/store/apps/details?id=com.gomdon)

<div class="description">
My side project that written in Python, crawling delivery requests from the shipping groups on Facebook, pushing them to Firebase server, then showing them on the React Native mobile app to help shipper can quickly pickup the request.
</div>

## Open Quiz [⤴](http://thanhnien.vnpost.vn)

<div class="description">
A quiz platform that I have coded in Django as a opensource project. It now has been used in some government organizations such as DKDNTW, VNPost.
</div>

## Viber Bot

<div class="description">
Facing the problem of requesting the permission for using Viber API, so I decided to take a hack with Viber database on Android platform, then do the Viber Bot with my own way. Now it is using in VNPost as the virtual assistant.
</div>

# Programming skills
---

## React Native
<div class="progress reactnative"></div><br/>

## React.js
<div class="progress reactjs"></div><br/>

## Django/Python
<div class="progress django"></div><br/>

## Go
<div class="progress go"></div><br/>

# English skills
---

Reading
<div class="progress reading"></div><br/>

Writing
<div class="progress writing"></div><br/>

Listening
<div class="progress listening"></div><br/>

Speaking
<div class="progress speaking"></div><br/>

# Other skills
---

#self-training &nbsp;&nbsp;
#ui/ux &nbsp;&nbsp;
#docker &nbsp;&nbsp; 
#graphql &nbsp;&nbsp;
#firebase &nbsp;&nbsp;
#android &nbsp;&nbsp;
#microservive &nbsp;&nbsp;
#devops &nbsp;&nbsp;
#facebook-api &nbsp;&nbsp;
#blockchain &nbsp;&nbsp;
#mysql &nbsp;&nbsp;
#sysadmin &nbsp;&nbsp;

# Interests
---

#game &nbsp;&nbsp;
#coding &nbsp;&nbsp;
#football &nbsp;&nbsp; 
#drawing &nbsp;&nbsp;
