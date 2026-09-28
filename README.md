# Online Course App

A Django web app for browsing and enrolling in online courses, with an exam and grading feature. Learners sign up, enroll in a course, answer its multiple-choice questions and see their score right away.

Built as the final project for the IBM Developer Skills Network Django course.

## Features

- **User accounts**: sign up, log in and log out
- **Course catalog**: the 10 courses with the most enrollments, each marked if you're enrolled
- **Enrollment**: enroll in a course to open its lessons and exam
- **Exams**: multiple-choice questions where a question can have more than one correct answer
- **Grading**: a question's points count only if you select exactly its correct choices. A score above 80/100 passes.
- **Admin**: manage courses, lessons, instructors, learners, questions and choices in the Django admin. Lessons are edited inside their course and choices inside their question.

## Tech stack

- Python 3.8, Django 4.2
- SQLite3 (the default; any database Django supports will work)
- Bootstrap templates
- Gunicorn, set up for Cloud Foundry deployment

## Data model

![Onlinecourse ER Diagram](static/media/course_images/onlinecourse_app_er.png)

| Model | Description |
| --- | --- |
| `Course` | Name, image, description, instructors and enrolled users |
| `Lesson` | An ordered piece of course content |
| `Instructor` / `Learner` | Profiles linked to a Django `User` |
| `Enrollment` | Joins a user to a course, with mode and rating |
| `Question` | An exam question for a course, worth `grade` points |
| `Choice` | An answer option for a question, flagged `is_correct` |
| `Submission` | A learner's exam attempt: an enrollment plus the choices selected |

## Getting started

```bash
git clone <repo-url>
cd course-rating-app

python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate

pip install -r requirements.txt

python manage.py migrate
python manage.py createsuperuser
python manage.py runserver
```

Then open:

- App: http://127.0.0.1:8000/onlinecourse/
- Admin: http://127.0.0.1:8000/admin/

To try the exam, create a course in the admin and add questions and choices to it. Make the question grades add up to 100. Then register as a learner, enroll in the course and submit the exam from the course page.

## Routes

All app routes are under `/onlinecourse/`.

| Path | View | Purpose |
| --- | --- | --- |
| `/` | `CourseListView` | Course catalog |
| `registration/` | `registration_request` | Sign up |
| `login/` / `logout/` | `login_request` / `logout_request` | Log in and log out |
| `<course_id>/` | `CourseDetailView` | Lessons and exam |
| `<course_id>/enroll/` | `enroll` | Enroll in the course |
| `<course_id>/submit/` | `submit` | Submit exam answers |
| `course/<course_id>/submission/<submission_id>/result/` | `show_exam_result` | Exam score and result |

## Project structure

```
myproject/        Django settings, root URLs, WSGI/ASGI
onlinecourse/     Models, views, URLs, admin and templates
static/           Static files and uploaded course images (static/media)
Procfile          Gunicorn start command
manifest.yml      Cloud Foundry deployment (app and static file server)
```

## Deployment

The repo is set up for Cloud Foundry (IBM Cloud by default). `manifest.yml` defines two apps: the Django app on the Python buildpack, and an nginx static file server on the staticfile buildpack. Before deploying:

1. Replace `host.domain` in `manifest.yml` with your route.
2. Set `DEBUG = False` and add your host to `ALLOWED_HOSTS` in `myproject/settings.py`.
3. Move `SECRET_KEY` out of the source code and into an environment variable.
4. Run `python manage.py collectstatic`, then `cf push`.

## License

Apache License 2.0. See [LICENSE](LICENSE).
