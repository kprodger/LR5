#LR5

##Chapter 19 - Mail in Rails

####Sending Mail Messages
The first step in getting Rails to send email messages is to generate a mailer. Running `rails generate mailer AwardMailer` at the command prompt creates a _mailer_ file - *app/mailers/award_mailer.rb*. The generator also provides the view directory as well as test and preview files necessary for a fully-featured messaging system.

Open *app/mailers/award_mailer.rb* in the text editor and add the following logic to the empty **AwardMailer** class...

		class AwardMailer < ApplicationMailer
			default from: "me@barnabasbulpett.com"

			def award_email(award)
			@award = award
			mail(:to => 'Barnabas Bulpett <barnabasbulpett@example.com>', :subject => "Award from Learning Rails")
			end
		end

In the *app/views/award_mailer* directory, create a new file called *award_email.text.erb*. Open the file and insert the following code to generate a basic plain text message:

		The <%= @award.name %> award for <%= @award.year %> has gone to <%= @award.student.name %>.

Create another file, *app/views/award_mailer/award_email.html.erb* with some markup for richer email messages:

		<!DOCTYPE html>
		<html>
			<head>
				<meta content="text/html; charset=UTF-8" http-equiv="Content-Type" />
			</head>
			<body>
				<h1>Award Notification</h1>
				<p>The <%= @award.name %> award for <%= @award.year %> has gone to
				<%= @award.student.name %>.</p>
			</body>
		</html>

Rails must be told to send email when awards are assigned. In *app/controllers/awards_controller.rb*, add this command within the `create` method like this...

		def create
			@award = @student.awards.build(award_params)
			# was @award = Award.new(params[:award])

			# Tell the AwardMailer to send a notice Email
			AwardMailer.award_email(@award).deliver
			respond_to do |format|
            ...
			end
		end

**NOTE:** At the time of this writing, ActionMailer for Rails 5 is still under development. Notice how the *AwardMailer* class in the above controller is generated to inherit from the *ApplicationMailer* class (as opposed to directly inheriting from ActionMailer). For now, we must **manually** create *app/mailers/application_mailer.rb* with the following code:

        class ApplicationMailer < ActionMailer::Base
            default from: "from@example.com"
        end

*config/environments/development.rb* can be configured with specific email credentials to make the mailer work. Refer to the text and also visit *http://guides.rubyonrails.org/action_mailer_basics.html#action-mailer-configuration-for-gmail* for configuration details. An example configuration has been provided in the *development.rb* example code for this chapter.

**NOTE:** *Google* has very strict security requirements for apps to connect to Gmail. This is a *good* thing. These security settings may be temporarily "overridden" to test your app. Consult the documentation.

####Previewing Mail

Open *test/mailers/previews/award_mailer_preview.rb* in the text editor and add some logic with the `award_email` method...

        # Preview all emails at http://localhost:3000/rails/mailers/award_mailer
        class AwardMailerPreview < ActionMailer::Preview
            def award_email
                AwardMailer.award_email(Award.first)
            end
        end

Now in the browser, navigate to **http://localhost:3000/rails/mailers** to check out what a generated "Award" email would look like. You can select the plain-text or HTML version.