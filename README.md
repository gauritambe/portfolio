<!DOCType>
<html><body>
<h1>Deploying portfolio on AWS using CI/CD tool like GitHub Actions</h1>
Required:
Make sure you have the following before you begin:

  - index.html, style.css, and RESUME files ready 
  - Git installed
  - Any code editor (e.g., VS Code)
  - AWS account (Free Tier or Pro)
  - GitHub account

<h3>Portfolio deployment steps</h3>
1.Set up the folder on code editor i.e index.html, style.css, and RESUME.

2.Create GitHub Repository in GitHub Acc
  - Give name as: portfolio-me
  - Set it Visibility as public
  - Create

3.Open code editor:
  - Open project folder in terminal and type cmds to push code into the GitHb repo (Right Click on folder and open in intergrated terminal)
  - In terminal type cmds as follows:</ul>
  -       git init
          git checkout -b main
          git add .
          git commit -m "First Commit"
          git remote add origin <your-repo-URL>
          git push origin main

            
4. Create AWS Account then go to the IAM service: ( Note: Configure your machine so that you can use awscli )
    - Create user as Devops and create Access keys(CLI) for that also and give administartor access. ( Download Access list .csv file )
      
5. In AWS account go to the S3 Bucket:
      - Create bucket:
            name: <give unique name> 
            Public access → Untick 
            Create bucket
      - Under the bucket → Properties → Static web hosting → Enable → index.html
      - permissions → Bucket policy:
      -                               {
                    "Version": "2012-10-17",
                    "Statement": [
                        {
                            "Sid": "PublicReadGetObject",
                            "Effect": "Allow",
                            "Principal": "*",
                            "Action": "s3:GetObject",
                            "Resource": "<arnOfYourBucket>/*"
                        }
                    ]
                }
          SAVE Changes
        - Find the S3 Website Endpoint in bucket properties and paste it in browser. ( This will show error cause the whole steps to upload file is in next step. )
          
6. Configure AWS secrets:
    - Go to the Settings tab in GitHub and on the left side find Secrets & variables → Actions → New Repository Secrets → 
          Name: AWS_ACCESS_KEY_ID   Value: cp your iam users access key from .csv file → SAVE
          Name: AWS_SECRET_ACCESS_KEY   Value: cp your iam users secret access key from .csv file → SAVE
                              
7. Open github for CI/CD:
      - Go to Actions tab → set up workflow yourself (A file will be created as main.yml)
      - In main.yml write CI/CD code
            name: Portfolio on aws deployment

            on: 
              push:
                branches:
                - main
            
            jobs:
              deploy:
                runs-on: windows-latest #Your Os name
                steps:
                - name: Checkout
                  uses: actions/checkout@v1
            
                - name: Configure AWS Credentials
                  uses: aws-actions/configure-aws-credentials@v1
                  with:
                    aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
                    aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
                    aws-region: ap-south-1 #Your region name
            
                - name: Deploy static site to S3 bucket
                  run: aws s3 sync . s3://<bucketName> --delete

8. PUSH again onto the repo so the task will get triggered and then have a look at your endpoint url the website is deployed
  - Make any change to your code and push it again:
       -    git add .
       -    git commit -m "Update portfolio"
       -    git push origin main
  - GitHub Actions will automatically deploy your site to S3.
  - Visit your S3 Website Endpoint to see your live portfolio!

Benifits:
- Fully automated deployment via GitHub Actions
- No manual upload required to S3
- Continuous updates — every push to main updates your live portfolio

</body>
</html>
  
