# Create administrative Web user from shell command line

There is a script called mkaccount in root home directory (/root). This is a simple one-liner that I've reformatted for legibility:
```cmd
/usr/share/homebase-accounts/regserver.pl \
                --webbased=true \
                --action=registration \
                --version=2.1.0 \
                --newloginid=true \
                --loginid=$1 \
                --stdinpassword \
                --quota=10000000 \
                --addadministrator=true
```
(Assuming you have ssh access or other access to root command line) In a pinch, this can be used to create an administrative web user, sort of a brute-force way of (re)gaining administrative access when passwords are lost, et cetera.

Let's say you want to create a user named justdoit with a password imeannow.

All you need to do, is
```bash
cd
echo 'imeannow' | ./mkaccount justdoit
```
This will run in a few seconds, echoing back its progress:
```
about to create account at /usr/share/homebase-accounts/regserver.pl line 659, <STDIN> line 1.
Added user justdoit.
done create account at /usr/share/homebase-accounts/regserver.pl line 665, <STDIN> line 1.
<oeserver_response>
<error number='0' text='success'/>
<groupid text='justdoit' />
<accountpaid value='false' />
</oeserver_response>
#
```
Now you will able to log in to the web interface as user 'justdoit', with password 'imeannow', with full administrative access!!!!

My recommendation would be to use this account only to reactivate your administrative account, log in as that administrative user, and delete the new user (justdoit).