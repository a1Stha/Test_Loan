# Test_Loan

To create a simple loan application using Django, we'll build a basic Django project with models, views, and templates for managing loan requests. In this example, we'll create a loan application where users can submit loan requests and view their status. Let's go through the steps:

### Step 1: Set Up Django Project

Make sure you have Django installed. You can install Django using pip if you haven't already:

```bash
pip install django
```

Now, let's create a new Django project called `loanapp`:

```bash
django-admin startproject loanapp
cd loanapp
```

### Step 2: Create Django App for Loan Requests

Within the `loanapp` directory, create a new Django app called `loans`:

```bash
python manage.py startapp loans
```

### Step 3: Define Loan Model

In the `loans` app, define the loan model in `models.py`:

```python
# loans/models.py
from django.db import models
from django.contrib.auth.models import User

class Loan(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    amount = models.DecimalField(max_digits=10, decimal_places=2)
    reason = models.TextField()
    is_approved = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return f"Loan Request of {self.user.username} - ${self.amount}"
```

### Step 4: Create Loan Views

Define views to handle loan requests and approvals in `loans/views.py`:

```python
# loans/views.py
from django.shortcuts import render, redirect
from .models import Loan
from django.contrib.auth.decorators import login_required

@login_required
def loan_request(request):
    if request.method == 'POST':
        amount = request.POST['amount']
        reason = request.POST['reason']
        loan = Loan.objects.create(user=request.user, amount=amount, reason=reason)
        return redirect('loan_status')

    return render(request, 'loans/loan_request.html')

@login_required
def loan_status(request):
    loans = Loan.objects.filter(user=request.user)
    return render(request, 'loans/loan_status.html', {'loans': loans})
```

### Step 5: Create Loan Templates

Create HTML templates for loan request and status in `loans/templates/loans/` directory.

`loan_request.html`:
```html
<!-- loans/templates/loans/loan_request.html -->
<h2>Loan Request</h2>
<form method="post">
    {% csrf_token %}
    <label for="amount">Amount:</label>
    <input type="number" name="amount" required><br><br>
    <label for="reason">Reason for Loan:</label>
    <textarea name="reason" required></textarea><br><br>
    <button type="submit">Submit</button>
</form>
```

`loan_status.html`:
```html
<!-- loans/templates/loans/loan_status.html -->
<h2>Loan Status</h2>
{% if loans %}
    <ul>
    {% for loan in loans %}
        <li>{{ loan }}</li>
    {% endfor %}
    </ul>
{% else %}
    <p>No loans requested yet.</p>
{% endif %}
```

### Step 6: Configure URLs

Configure URLs to map views in `loans/urls.py`:

```python
# loans/urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('request/', views.loan_request, name='loan_request'),
    path('status/', views.loan_status, name='loan_status'),
]
```

Include these URLs in the main `urls.py` of the project (`loanapp/urls.py`):

```python
# loanapp/urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('loans/', include('loans.urls')),
]
```

### Step 7: Run Migrations and Start Django Server

Apply migrations to create the `Loan` model table in the database:

```bash
python manage.py makemigrations
python manage.py migrate
```

Now, start the Django development server:

```bash
python manage.py runserver
```

Visit `http://127.0.0.1:8000/admin/` to create a superuser and then navigate to `http://127.0.0.1:8000/loans/request/` to access the loan request form.

### Summary

This example demonstrates the basic setup for a loan application using Django. Users can submit loan requests, which are stored in the database, and view their loan status. You can further enhance this application by adding features like loan approval, authentication, additional user profiles, etc., depending on your requirements.
