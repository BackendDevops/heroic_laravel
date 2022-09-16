# Heroic Laravel Personal Financials Tracking system




## Table of Contents

* [Demo](#demo)
* [Installation](#installation)
* [Documentation](#documentation)
* [File Structure](#file-structure)
* [Browser Support](#browser-support)





# Installation

## Prerequisites

If you don't already have an Apache local environment with PHP and MySQL, use one of the following links:

 - Windows: https://updivision.com/blog/post/beginner-s-guide-to-setting-up-your-local-development-environment-on-windows
 - Linux: https://howtoubuntu.org/how-to-install-lamp-on-ubuntu
 - Mac: https://wpshout.com/quick-guides/how-to-install-mamp-on-your-mac/

Also, you will need to install Composer: https://getcomposer.org/doc/00-intro.md

## Laravel
1. Download the project’s zip then copy and paste  folder in your projects folder. Rename the folder to your project’s name
2. Make sure you have Node and Composer locally installed.
3. Run the following command in order to download all the project dependencies. `composer install`
4. In your terminal run `npm install`
5. Copy `.env.example` to `.env` and updated the configurations (mainly the database configuration)
6. In your terminal run `php artisan key:generate`
7. Run `php artisan migrate --seed` to create the database tables and seed the roles and users tables
8. Run `php artisan storage:link` to create the storage symlink (if you are using **Vagrant** with **Homestead** for development, remember to ssh into your virtual machine and run the command from there).


## Usage

Register a user or login using admin@admin.com and secret and start testing the Laravel app (make sure to run the migrations and seeders for these credentials to be available).
Make sure to run the migrations and seeders for the above credentials to be available.

Make sure to run the migrations and seeders for the above credentials to be available.

Besides the dashboard and the auth pages this application also has an edit profile page. All the necessary files (controllers, requests, views) are installed out of the box and all the needed routes are added to `routes/web.php`. Keep in mind that all of the features can be viewed once you login using the credentials provided above or by registering your own user.

### Dashboard

You can access the dashboard either by using the "**Dashboard**" link in the left sidebar or by adding **/dashboard** in the URL.


### Sign in

You have the option to log in using the email and password. To access this page, just click the "**Page examples/ Sign in**" link in the left sidebar or add **/login** in the URL.

The `app/Http/Livewire/Auth/Login.php` handles the log in process and validation.

```
   protected $rules = [
        'email' => 'required|email',
        'password' => 'required',
    ];

    public function login()
    {
        $credentials = $this->validate();
        return auth()->attempt($credentials)
                ? redirect()->intended('/profile')
                : $this->addError('email', trans('auth.failed'));
    }
```

### Sign up

You have the option to register an user using the email and password. To access this page, just click the "**Page examples/ Sign up**" link in the left sidebar or add **/register** in the URL.

The `app/Http/Livewire/Auth/Register.php` handles the register process and validation.

```
    public function register()
    {
        $this->validate([
            'email' => 'required',
            'password' => 'required|same:passwordConfirmation|min:6',
        ]);

        $user = User::create([
            'email' =>$this->email,
            'password' => Hash::make($this->password),
            'remember_token' => Str::random(10),
        ]);

        auth()->login($user);

        return redirect('/profile');
    }
```
### Forgot password

You have the option to send an email containing the password reset link to an user. To access this page, just click the "**Page examples/ Forgot password**" link in the left sidebar or add **/forgot-password** in the URL.

The `app/Http/Livewire/ForgotPassword.php` handles the email submission process.

```
    public function recoverPassword() {
        $this->validate();
        $user=User::where('email', $this->email)->first();
        $this->notify(new ResetPassword($user->id));
        $this->mailSentAlert = true;
        }
    }
```

The `app/Notifications/ResetPassword.php` handles the email submission itself. Here you can edit the overall layout of the email.
```
    public function toMail($notifiable)
    {
        $url = URL::signedRoute('reset-password', ['id' => $this->token]);
        return (new MailMessage)
                    ->subject('Reset your password')
                    ->line('Hey, did you forget your password? Click the button to reset it.')
                    ->action('Reset Password', $url)
                    ->line('Thank you for using our application!');
    }

```

### Reset password

The email sent through the forgot password process will send the user to an unique link containing the password reset form. To access an example of this page, just click the "**Page examples/ Reset password**" link in the left sidebar or add **/reset-password-example** in the URL.

The `app/Http/Livewire/ResetPassword.php` handles the password reset process and validation.

```

    public function resetPassword() {
        $this->validate();
        $existingUser = User::where('email', $this->email)->first();
        if($existingUser && $existingUser->id == $this->urlId) {
            $existingUser->update([
                'password' => Hash::make($this->password)
            ]);
            $this->isPasswordChanged = true;
            $this->wrongEmail = false;
        }
        else {
            $this->wrongEmail = true;
        }
    }
    
```

### User Profile

You have the option to edit the current logged in user's profile information (name, email, profile picture) and password. To access this page, just click the "**Profile**" link in the left sidebar or add **/profile** in the URL.

The `app/Http/Livewire/Profile.php` handles the update of the user information and password.

```
    public function mount() { $this->user = auth()->user(); }

    public function save()
    {
        $this->validate();

        $this->user->save();

        $this->showSavedAlert = true;
            
        }
    }
```

If you input the wrong data when editing the profile, don't worry. Validation rules have been added to prevent this.

```
    protected $rules = [
        'user.first_name' => 'max:15',
        'user.last_name' => 'max:20',
        'user.birthday' => 'date_format:Y-m-d',
        'user.email' => 'email',
        'user.phone' => 'numeric',
        'user.gender' => '',
        'user.address' => 'max:20',
        'user.number' => 'numeric',
        'user.city' => 'max:20',
        'user.zip' => 'numeric',
    ];

```


## Documentation


## File Structure
Within the download you'll find the following directories and files:

```

├── components
│   ├── buttons.blade.php                       # Buttons page
│   ├── forms.blade.php                         # Forms page
│   ├── modals.blade.php                        # Modals page
│   ├── notifications.blade.php                 # Notifications page
│   └── typography.blade.php                    # Typography page
├── dashboard.blade.php                         # Dashboard
├── layouts
│   ├── app.blade.php                           # Including layouts based on routes
│   ├── base.blade.php                          # All the styles and scripts included
│   ├── footer2.blade.php                       # Footer for pages without sidenav
│   ├── footer.blade.php                        # Footer for pages with sidenav
│   ├── nav.blade.php                           # Nav for mobile view 
│   ├── sidenav.blade.php                       # The sidebar menu
│   └── topbar.blade.php                        # Search bar, notifications and user area
├── livewire                                    # All the pages that are using livewire functionality
│   ├── auth                                    # Handles auth routes (login and register)
│   │   ├── login.blade.php                     
│   │   └── register.blade.php
│   ├── forgot-password.blade.php               # Handles the forgot-password form
│   ├── logout.blade.php                        # Logout functionality
│   ├── profile.blade.php                       # Profile page
│   ├── reset-password.blade.php                # Handles the reset password form
│   └── users.blade.php                         # Users table
├── upgrade-to-pro.blade.php                    # Upgrade to pro page
├── lock.blade.php                              # Lock page
└── transactions.blade.php                      # Transactions page
├── 404.blade.php                               # Error 404 page
├── 500.blade.php                               # Error 500 page
├── bootstrap-tables.blade.php                  # Bootstrap tables page                
```

## Browser Support

At present, we officially aim to support the last two versions of the following browsers:

<img src="https://s3.amazonaws.com/creativetim_bucket/github/browser/chrome.png" width="64" height="64"> <img src="https://s3.amazonaws.com/creativetim_bucket/github/browser/firefox.png" width="64" height="64"> <img src="https://s3.amazonaws.com/creativetim_bucket/github/browser/edge.png" width="64" height="64"> <img src="https://s3.amazonaws.com/creativetim_bucket/github/browser/safari.png" width="64" height="64"> <img src="https://s3.amazonaws.com/creativetim_bucket/github/browser/opera.png" width="64" height="64">


## Change log

Please see the [changelog](CHANGELOG.md) for more information on what has changed recently.

