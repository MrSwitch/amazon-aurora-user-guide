# Deleting a blue/green deployment<a name="blue-green-deployments-deleting"></a>

You can delete blue/green deployment before or after switching it over\.

When you delete a blue/green deployment before switching it over, Amazon RDS takes the following actions before deleting it:

1. Stops replication from the blue environment to the green environment\.

1. Optionally deletes the DB cluster in the green environment\.

   If you choose to delete the DB cluster in the green environment, make sure deletion protection isn't turned on for it\.

   If you don't delete the DB cluster in the green environment, it is retained, but it is no longer part of a blue/green deployment\.

**Important**  
Deleting a blue/green deployment doesn't affect the blue environment\.

You can delete a blue/green deployment using the AWS Management Console, the AWS CLI, or the RDS API\.

## Console<a name="blue-green-deployments-deleting-console"></a>

**To delete a blue/green deployment**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the blue/green deployment that you want to delete\.

1. For **Actions**, choose **Delete**\.

   The **Delete Blue/Green Deployment?** window appears\.  
![\[Delete blue/green deployment\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/blue-green-deployment-delete-aurora.png)

   To delete the green databases, select **Delete the green databases in this Blue/Green Deployment**\.

1. Enter **delete me** in the box\.

1. Choose **Delete**\.

## AWS CLI<a name="blue-green-deployments-deleting-cli"></a>

To delete a blue/green deployment by using the AWS CLI, use the [delete\-blue\-green\-deployment](https://docs.aws.amazon.com/cli/latest/reference/rds/delete-blue-green-deployment.html) command with the following options:
+ `--blue-green-deployment-identifier` – The identifier of the blue/green deployment to be deleted\.
+ `--delete-target` – Specifies that the DB cluster in the green environment is deleted\.
+ `--no-delete-target` – Specifies that the DB cluster in the green environment is retained\.

**Example Delete a blue/green deployment and the DB cluster in the green environment**  
For Linux, macOS, or Unix:  

```
aws rds delete-blue-green-deployment \
    --blue-green-deployment-identifier bgd-1234567890abcdef \
    --delete-target
```
For Windows:  

```
aws rds delete-blue-green-deployment ^
    --blue-green-deployment-identifier bgd-1234567890abcdef ^
    --delete-target
```

**Example Delete a blue/green deployment but retain the DB cluster in the green environment**  
For Linux, macOS, or Unix:  

```
aws rds delete-blue-green-deployment \
    --blue-green-deployment-identifier bgd-1234567890abcdef \
    --no-delete-target
```
For Windows:  

```
aws rds delete-blue-green-deployment ^
    --blue-green-deployment-identifier bgd-1234567890abcdef ^
    --no-delete-target
```

## RDS API<a name="blue-green-deployments-deleting-api"></a>

To delete a blue/green deployment by using the Amazon RDS API, use the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteBlueGreenDeployment.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteBlueGreenDeployment.html) operation with the following parameters:
+ `BlueGreenDeploymentIdentifier` – The identifier of the blue/green deployment to be deleted\.
+ `DeleteTarget` – Specify `TRUE` to delete the DB cluster in the green environment or `FALSE` to retain it\.