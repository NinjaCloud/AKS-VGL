# **Steps to Apply Built-in Azure Policies for an AKS Cluster Using Azure Portal**

Follow these steps to apply built-in **Azure Policies** to an **Azure Kubernetes Service (AKS)** cluster using the **Azure Portal**.

---

## **Step 1: Sign in to the Azure Portal**
1. Open [Azure Portal](https://portal.azure.com).
2. Sign in with your Azure account.

---

## **Step 2: Navigate to Azure Policy**
1. In the **Azure Portal**, search for **Policy** in the search bar and select **Policy** from the results.
2. This will open the **Azure Policy** dashboard.

---

## **Step 3: Assign a Built-in Policy to AKS**
1. In the **Policy** page, click on **Assignments** in the left menu.
2. Click on **Assign Policy** (at the top).
3. In the **Basics** tab:
   - **Scope**: Click on the **ellipsis (… button)** and select the **Subscription** and **Resource Group** where your AKS cluster is deployed.
   - **Policy definition**: Click on **Select Policy** and search for "Kubernetes".
   - Choose a **built-in policy** such as:
     - **Enforce HTTPS Ingress Only on AKS**
     - **Restrict Privileged Containers in AKS**
     - **Require Pod Security Policies on AKS**
4. Click **Next**.

---

## **Step 4: Configure Policy Parameters (If Required)**
1. Some policies require additional parameters. If prompted, configure the necessary settings (e.g., enabling HTTPS).
2. Click **Next**.

---

## **Step 5: Review and Assign**
1. Review the policy details.
2. Click **Assign** to apply the policy.

---

## **Step 6: Verify Policy Assignment**
1. Go back to the **Policy -> Assignments** page.
2. Look for the newly assigned policy in the list.

---

## **Step 7: Check Compliance Status**
1. In the **Policy** dashboard, go to **Compliance**.
2. Search for the assigned policy.
3. Check whether your AKS cluster is **compliant** or **non-compliant**.
4. If non-compliant, Azure will provide remediation steps.

---

## **Step 8: Remediate Non-Compliant Resources (If Needed)**
1. In the **Compliance** tab, find the non-compliant policy.
2. Click **Create Remediation Task** to fix the issue automatically.

---

## **Step 9: Monitor Policy Compliance**
1. In **Policy -> Compliance**, periodically check if your AKS cluster is following the assigned policies.
2. You can set up alerts for policy violations if required.

---

### **Conclusion**
You have successfully applied a built-in **Azure Policy** to your **AKS cluster** using the **Azure Portal**. These policies help enforce security and best practices in your Kubernetes environment.

Would you like me to add screenshots or any more details? 😊
