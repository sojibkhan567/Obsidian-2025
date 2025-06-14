Using this method in livewire we can change password. We also send email.

```php
public function updatePassword() {
        $user = User::findOrFail(Auth::id());
        $this->validate([
            'current_password' => [
                'required',
                'min:6',
                function ($attribute, $value, $fail) use ($user) {
                    if (!Hash::check($value, $user->password)) {
                        return $fail(__('Your current password does not match your record.'));
                    }
                }
            ],
            'new_password' => 'required|confirmed|min:6'
        ]);

        //update new password in database
        $updated_new_password = $user->update([
            'password' => Hash::make($this->new_password)
        ]);

        if ($updated_new_password) {
            //Send notification email to this user email
            $data = array(
                'new_password' => $this->new_password,
                'user' => $user
            );
            $mail_body = view('admin-dashboard.email-templates.password-changes-template', $data)->render();
            $mail_config = array(
                'from_address' => 'hello@gmail.com',
                'recipient_address' => $user->email,
                'recipient_name' => $user->name,
                'subject' => 'Password Changed',
                'body' => $mail_body
            );
            CMail::send($mail_config);

            //logout and redirect user to login page
            Auth::logout();
            Session::flash('info', "Your password have been changed successfully. Please login with your new password.");
            $this->redirectRoute('admin.login');
        } else {
            $this->dispatch('showToastr', ['type' => 'error', 'message' => 'Something went wrong.']);
        }
    }
```