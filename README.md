### How to create a GitHub repo using Terraform code

There are times while writing code you want to quickly create a Github repository. Doing it everytime from the web browser, especially for creating multiple repositories, can be mundane. Fortunately, Terraform comes to the rescue and offers Github repo creation through Terraform code. 

1. Install Terraform 
2. Generate a personal access token if you haven't already.  
    GitHub > Settings > Developers settings > Personal access token  
    Set the token as an environment variable on your shell for secure reasons.  
    ```bash
    export TF_VAR_ghtoken="paste the token here"
    ```
2. Use the code in new-repo.tf (change "name" and "organization" accordingly)
3. Terraform apply and verify 



#### Code in new-repo.tf:
```hcl
provider "github" {
  token        = "${var.ghtoken}"
  organization = "samit-a" #enter github organization/account name here
  version      = "~> 1.3"
}

resource "github_repository" "repo" {
  name               = "tf-github-example"
  description        = "This repo was created using Terraform"
  gitignore_template = "Terraform"
  has_issues         = true
  has_wiki           = true
  private            = true

  lifecycle {
    prevent_destroy = true #to prevent repo destruction on terraform destroy
  }
}

#Set as environment variable
variable "ghtoken" {}

#Returns the ssh url for you to git clone
output "ssh_clone_url" {
  value = "${github_repository.repo.ssh_clone_url}"
}
```
- Variable "ghtoken" is blanked out because it's an environment variable. When you hit Terraform apply, if the variable isn't declared in the .tf file, Terraform moves on and looks to see if it's set as an environment variable on your shell. It is, so the plan/apply succeeds.  
- In the future I will make this a module to make the process even simpler and reduce the lines of code required.
