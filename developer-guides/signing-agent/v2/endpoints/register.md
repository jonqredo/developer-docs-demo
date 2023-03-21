---
hide:
  - navigation
  - toc
proofedDate: 20221217
author: HB
comment: There is no CICD between repo controlling these services and this specification. Main branch of signing agent holds the swagger. todo link the 2 swaggers in CICD.
--- 

<html>
  <head>
    <title>Qredo Signing Agent Registration v1</title>
    {% comment %} needed for adaptive design {% /comment %}
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <link
      href="https://fonts.googleapis.com/css?family=Montserrat:300,400,700|Roboto:300,400,700"
      rel="stylesheet"
    />
    <style>
      body {
        padding: 0;
        margin: 0;
      }
      .md-typeset ol li, .md-typeset ul li {
          margin-bottom: 0;
          margin-left: 0;
      }
      .md-content__inner h1 {
        display:none;
      }
      .md-content__inner h1.sc-fujyAs {
        display:block;
      }
      .md-grid {
        max-width:100% !important;
      }
      .md-content__inner {
        margin: 0;
        padding: 0;
      }
      .md-content {
        overflow:unset;
        width:100%;
      }
      article.md-content__inner p {
        margin-block:0;
      }
      article.md-content__inner p br{
        display:none;
      }
      article.md-content__inner p:nth-child(2),
      article.md-content__inner p:nth-child(4) {
        display:none;
      }
      .md-main__inner {
        margin-top:0;
      }
      .md-content__inner:before {
        display:none;
      }
      .hqgvLZ {
        height: calc(100vh - 60px) !important;
        top: 60px !important;
        padding-top: 30px;
      }
    </style>


  </head>
  <body>
    {% comment %}
    Redoc element with link to your OpenAPI definition
    {% /comment %}
    <redoc spec-url="/register.yaml"></redoc>
    {% comment %}
    Link to Redoc JavaScript on CDN for rendering standalone element
    {% /comment %}
    <script src="https://cdn.redoc.ly/redoc/latest/bundles/redoc.standalone.js"></script>
    <script>
      element = document.getElementsByClassName('md-content__inner');
      element[0].classList.remove("md-typeset");
    </script>
  </body>
</html>
