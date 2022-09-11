---
title: "The Cloud Resume Challenge - AWS"
date: 2022-08-26T20:19:08-05:00
draft: false
---

## The Challenge

Ever since Forrest Brazeal released his original [Cloud Resume Challenge](https://cloudresumechallenge.dev/docs/the-challenge/aws/), I've been curious to take it for a spin. As a former educator who pivoted into a career in technology, with the vast majority of it in cloud, it hit all the sweet spots for me. Clear, concise, project-based, relevant, and challenging enough to demonstrate all the competencies that a cloud engineer would need to be successful.

I finally sat down to do the challenge myself. I've been in this field for a while and had all the theoretical knowledge, plus I'd done much of it in various work projects before. This should be easy, right?

Hoo boy, not right. Let's dive in.

## The Process

Right off the bat, I ran into some issues. When I first started in technology I had an inkling I'd be doing development, so I focused on [FreeCodeCamp's](https://freecodecamp.com) outstanding HTML, CSS, and JavaScript certifications (since then it's expanded to Python and so much more). I'd recently updated my resume in Word so it should be straightforward to convert to HTML - that part turned out to be true.

Then my old friend CSS reared his ugly head, reminding me why I pivoted to infrastructure and DevOps in the first place! I did manage to get something functional together but it was a great reminder why you should pay an expert to do things you don't like. Frontend developers, you'll always have my respect (and my cash 💰).

Once I got the basic site off the ground I wrote up the infrastructure using the [AWS Cloud Development Kit](https://aws.amazon.com/cdk/). CDK, as it is reverently known to its practitioners, is a neat way to create infrastructure using programming languages instead of abstractions of JSON (YAML, HCL) or JSON itself. I'd been working on getting more competent with it since it's pretty neat and likely to be the future of Infrastructure as Code. Some of the specifics of CDK were new to me but I had extensive practice with CloudFormation and Terraform, as well as deploying static sites to S3 with CloudFront in a private configuration, so that part went smoothly. Some of the nuances and challenges of TypeScript and CDK itself took time to get comfortable with.

Next up was the website counter code. The bones of it were straightforward enough. The real issue came when I started wiring it up to the API - that was easy enough to write since I'm proficient in Python. It was my other old friend: CORS! This project was quickly becoming a "Greatest Hits" track of the terrors of my short stint seeking a Full Stack Development job.

Once everything finally behaved the way I wanted it to, setting up GitHub Actions to deploy everything was pretty fun. I [set up OIDC for GitHub](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services) through CDK, which meant that I didn't need to keep long-lived AWS credentials around. This portion wasn't as well-documented as I'd like, so it's very likely I'll be sharing a blog post on just this topic some time in the future.

Once that was done I used the GitHub Actions Marketplace to find a straightforward way to invalidate my CloudFront cache when changes were depoyed. Ultimately I'd like to continue to tweak my CI/CD pipeline to only deploy certain CDK Stacks when a change has been made to it, as well as only invalidate the CloudFront cache when the site has been updated. The changes are infrequent enough that what I have in place now will work without being too expensive.

Finally, I didn't have a blog in place, and I'm trying to move away from Medium (a topic for another day). I tried out [Hugo](https://gohugo.io/) because it looked straightforward enough to use and I already frequently write in Markdown. It was very easy to get started, pick a template, and hook up to [Netlify](https://www.netlify.com/). That's where you're reading this blog!

## Things I'd Do Differently

I came into this challenge with a little more hubris than was warranted - I forgot what a pain CSS and CORS can be, and I didn't respect the broad spectrum of skills that the Cloud Resume Challenge covers, so I tried to make too many adjustments. When I do this again for GCP (my other cloud of choice) I'll stick to the letter of the rules to start with, then make changes after the MVP is out the door.

I'd also probably use Hugo for the resume itself. As much fun as it was to dust off my old, poor CSS skills, Hugo + Netlify are so simple to use and significantly better results than I'm likely to get myself without spending much more time on it.

Finally, as much as I love this challenge as a portfolio project, I would _absolutely not_ deploy a simple resumé site with a JavaScript counter like this for a client! Unless they had every intention of expanding the site functionality to leverage the benefits of serverless, the solution is over-engineered for production. This isn't a criticism of the challenge or of Forrest, who certainly knows this, but something to call out to folks newer to the industry. It's easy to get wrapped up in all the cool things you can do in the cloud. One of the hallmarks of a more seasoned engineer is understanding when _not_ to do the cool thing.

## Things I Learned/Rembered

The biggest learning experience for me on this project was definitely re-negotiating how CORS works. I'd never used API Gateway outside of a lab environment or tweaking existing setups, so I did struggle a bit. The hardest part was remembering that CORS needs to be set properly not just on the API Gateway itself, but also within the Lambda code.

I also ran into some interesting challenges working with AWS Certificate Manager, which required setting up a separate stack in the CDK. Most of my resources were in us-east-2, but ACM requires that the certificate be in us-east-1, at least for CloudFront. That led to a few unruly challenges referencing the ACM ARN in my us-east-2 stacks - CDK seems to only support referencing existing certificates by ARN and CloudFormation doesn't support outputs across regions. I ended up hardcoding the ARN in my us-east-2 stack. I don't love that solution but short of submitting a PR to the CDK project to allow for other options, it's what needed to be done.

This project allowed me to really dial in a repeatable IaC solution for OIDC connecting to GitHub specifically. A client I had worked with in the past refused to use OIDC so when we migrated their code to GitHub Enterprise Cloud, I created a Terraform module for private GitHub Actions runners. The opportunity to get familiar with GitHub's preferred OIDC pattern in this challenge was welcome.

Finally, getting some basic GitHub Actions in place to do a bare-bones deployment of CDK into an AWS environment was a fun exercise. CDK prefers using their CDK Pipeline L3 construct for all deployments, but in restrictive environments you don't always get permission to use it. The biggest challenge with my GitHub Action was making an adjustment to the Python requirements so that the Alpha version of the Python Lambda L3 Construct worked properly.

Due to a combination of defaults in [Poetry](https://python-poetry.org) and the way that Construct packages the Lambda, the exported `requirements.txt` had a mixture of requirements with and without hashes. This causes deployment to fail. The solution was to install Poetry in the Action and export the requirements without hashes explicitly, saving the existing files as a backup. That also allows for the deployment to work properly even if someone forgets to export the newest updates to the Poetry project.

The relevant lines look like this:

```yaml
curl -sSL https://raw.githubusercontent.com/python-poetry/poetry/master/get-poetry.py | python -
source $HOME/.poetry/env
poetry export --without-hashes > requirements.txt
mv poetry.lock poetry.lock.bak
mv pyproject.toml pyproject.toml.bak
```

Are there better ways to handle it? Yes. Does this work? Also yes.

## Conclusion

Even though I'd already done most of the things that the Cloud Resume Challenge asks for, I still learned quite a bit from doing a project that combines all of them together. I also have an even healthier respect for folks new to the cloud space who successfully complete this challenge. Their tenacity and skills are most certainly enough for any entry-level cloud engineer position and they are well-positioned for continued future success.

Thanks to Forrest for putting this challenge together and creating an excellent on-ramp for people transitioning to cloud!
