// get a new ticket
def newTicketUrl = 'https://customdev.journaltech.com/api/xml/Ticket1.xml';
def newTicket = new XmlParser().parse(newTicketUrl);

// create a new person record
Person newPerson = new Person();
newPerson.firstName = newTicket.FirstName.text();
newPerson.middleName = newTicket.MiddleName.text();
newPerson.lastName = newTicket.LastName.text();
newPerson.saveOrUpdate();

// add the drivers license number to the person record
Identification dlNumber = new Identification();
dlNumber.identificationClass = "STID";  // STID: State Identification
dlNumber.identificationType = "DL";     // DL:   Driver's License Number
dlNumber.identificationNumber = newTicket.DriversLicenseNumber.text();
dlNumber.associatedPerson = newPerson;
dlNumber.saveOrUpdate();

// create the traffic newTicket case
Case newCase = new Case();
newCase.caseNumber = newTicket.newTicketID.text();
newCase.caseType = "TRAFFIC";
newCase.saveOrUpdate();

// add person to the case as a newParty
Party newParty = new Party();
newParty.case = newCase;
newParty.partyType = 'DEF'; // DEF: Defendant
newParty.person = newPerson;
newParty.saveOrUpdate();

// add the newCharge
Charge newCharge = new Charge();
newCharge.associatedParty = newParty;
newCharge.chargeType = "TRAF";  // TRAF: Traffic Citation
newCharge.description = newTicket.ViolationDesc.text();
newCharge.chargeNumber = newTicket.StatuteCode.text();
newCharge.location = newTicket.Location.text();
newCharge.saveOrUpdate();


// done!


