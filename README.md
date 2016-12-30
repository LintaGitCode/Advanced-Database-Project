# Advanced-Database-Project - View messages specific to a customer
Graduate program(MS in Information Systems) project
Create or replace PROCEDURE View_Messages (customerID IN number, msgDate IN date ) IS
a_id account.aid%type; 

Msg_Body Message.Message_Body%Type;
Cursor C1 Is Select  message_body, a.aid  From message m, account a
where m.aid = a.aid and a.cid = customerID and m.message_date >= msgDate ;

BEGIN
Open C1;
Loop
	fetch C1 into msg_body,a_id;
	IF C1%notfound THEN
		dbms_output.put_line('No messages to display for specified account and date combination.');
	ELSE
		Dbms_Output.Put_Line('Account Id	'||'Message Body');
	END IF;
	dbms_output.put_line(a_id||'					'||msg_body);
	exit when C1%notfound;
		
End Loop;
CLOSE C1;

End;

