//Правекс_Внутрішні 408
function onCardInitialize() {
  onDisbleStatus();
  setControlforProps();
  setControlforSecretary();
  checkVisibleEmployeeUnit();
  setControlResponsible();
  checkVisibleFamiliarization();
  setControlRegVisible();
setControlReg();
//setControlforPropsSendEmail2();
  //setControlLineAndTime1();
  //onChangeLevel();
  setPropDirector();
setPropSignatories1();
}

function checkVisibleFamiliarization() {
  var toAttention = EdocsApi.getAttributeValue("toAttention");
  changeVisibleControl("Familiarization", toAttention.value == "true", false);

  var toAttention1 = EdocsApi.getAttributeValue("toAttention1");
  changeVisibleControl("Familiarization1", toAttention1.value == "true", false);
}

function onChangeType() {
  EdocsApi.setAttributeValue({ code: "Level", value: null, text: null });
  EdocsApi.setAttributeValue({ code: "Levell", value: null, text: null });
  EdocsApi.setAttributeValue({ code: "Conf", value: null, text: null });
  EdocsApi.setAttributeValue({ code: "Conf2", value: null, text: null });
  //changeVisibleDirector(false);
}

function onChangeLevel() {
  debugger;
  var levelAttr = EdocsApi.getAttributeValue("Level");
  var isLevelVisible =
    levelAttr &&
    (levelAttr.value == "24" ||
      levelAttr.value == "29" ||
      levelAttr.value == "13" ||
      levelAttr.value == "25" ||
      levelAttr.value == "26" ||
      levelAttr.value == "12");
  //var isLevelVisible = levelAttr && (levelAttr.value=='29');

  changeVisibleDirector(isLevelVisible);
  if (isLevelVisible) {
    if (
      levelAttr.value == "24" ||
      levelAttr.value == "29" ||
      levelAttr.value == "13"
    ) {
      setDirector(levelAttr.value);
    }
    if (levelAttr.value == "26" || levelAttr.value == "12" ||
      levelAttr.value == "25") {
      setDirectorLevel1(levelAttr.value);
    }
  }
  setLevelEnValue(levelAttr);
}

function setLevelEnValue(levelAttr) {
  if (levelAttr && levelAttr.value) {
    var levelEnAttr = EdocsApi.getAttributeValue("Levell");
    levelEnAttr.value = levelAttr.value;

    var dic = EdocsApi.getDictionaryItemData("Level2", levelAttr.value);
    var text = EdocsApi.findElementByProperty(
      "code",
      "kb3v",
      dic.attributes
    ).value;
    levelEnAttr.text = text;
    EdocsApi.setAttributeValue(levelEnAttr);
  }
}

function setDirector(levelAttrValue) {
  debugger;
  var directorValue = "[]";
  var atribute = { code: "Signatories1" };
  var directorText = null;
  if (
    levelAttrValue === "24" ||
    levelAttrValue === "29" ||
    levelAttrValue === "13"
  ) {
    var unitEmployee = EdocsApi.findEmployeeSubdivisionByLevelAndEmployeeID(
      CurrentUser.employeeId,
      2
    );
    if (unitEmployee && unitEmployee.managerId) {
      var manager = EdocsApi.getEmployeeDataByEmployeeID(
        unitEmployee.managerId
      );
      if (manager) {
        directorText = manager.positionName + "\t" + unitEmployee.managerName;
        var director = [];
        director.push({
          id: 0,
          employeeId: unitEmployee.managerId,
          index: 0,
          employeeName: unitEmployee.managerName,
          positionName: manager.positionName,
        });
        EdocsApi.setAttributeValue({
          code: "InitiatorHeadName",
          value: unitEmployee.managerName,
        });
        EdocsApi.setAttributeValue({
          code: "InitiatorHeadPosition",
          value: manager.positionName,
        });
        directorValue =
          '[{"id":0,"employeeId":"' +
          unitEmployee.managerId +
          '","index":0,"employeeName":"' +
          unitEmployee.managerName +
          '","positionName":"' +
          manager.positionName +
          '"}]';
        //directorValue = JSON.stringify(director);

        atribute.value = manager.phone3;
        EdocsApi.setAttributeValue(atribute);
      }
    }
  }
  EdocsApi.setAttributeValue({
    code: "Director",
    value: directorValue,
    text: directorText,
  });
}

function setDirectorLevel1(levelAttrValue) {
  debugger;
  var directorValue = "[]";
  var atribute = { code: "Signatories1" };
  var directorText = null;
  if (levelAttrValue === "26" || levelAttrValue === "12" || levelAttrValue === "25") {
    var unitEmployee = EdocsApi.findEmployeeSubdivisionByLevelAndEmployeeID(
      CurrentUser.employeeId,
      1
    );
    if (unitEmployee && unitEmployee.managerId) {
      var manager = EdocsApi.getEmployeeDataByEmployeeID(
        unitEmployee.managerId
      );
      if (manager) {
        directorText = manager.positionName + "\t" + unitEmployee.managerName;
        var director = [];
        director.push({
          id: 0,
          employeeId: unitEmployee.managerId,
          index: 0,
          employeeName: unitEmployee.managerName,
          positionName: manager.positionName,
        });
        EdocsApi.setAttributeValue({
          code: "InitiatorHeadName",
          value: unitEmployee.managerName,
        });
        EdocsApi.setAttributeValue({
          code: "InitiatorHeadPosition",
          value: manager.positionName,
        });
        directorValue =
          '[{"id":0,"employeeId":"' +
          unitEmployee.managerId +
          '","index":0,"employeeName":"' +
          unitEmployee.managerName +
          '","positionName":"' +
          manager.positionName +
          '"}]';
        //directorValue = JSON.stringify(director);

        atribute.value = manager.phone3;
        EdocsApi.setAttributeValue(atribute);
      }
    }
  }
  EdocsApi.setAttributeValue({
    code: "Director",
    value: directorValue,
    text: directorText,
  });
}

function setControlforSecretary() {
  var Details = EdocsApi.getControlProperties("Details");
  var Details2 = EdocsApi.getControlProperties("Details2");

  var condition = false;
  var stage = EdocsApi.getCaseTaskDataByCode("Secretary");

  if (stage) {
    condition =
      stage.state == "assigned" ||
      stage.state == "inProgress" ||
      stage.state == "completed" ||
      stage.state == "delegated";
  }

  Details.hidden = !condition;
  Details.required = true;
  Details2.hidden = !condition;
  Details2.required = true;
  EdocsApi.setControlProperties(Details);
  EdocsApi.setControlProperties(Details2);
}
function setControlResponsible() {
  var stage = EdocsApi.getCaseTaskDataByCode("ResponsibleTask");
  debugger;
  if (stage) {
    var condition =
      stage.state == "assigned" ||
      stage.state == "inProgress" ||
      stage.state == "completed" ||
      stage.state == "delegated";
    if (condition) {
      EdocsApi.setControlProperties({
        code: "Responsible",
        hidden: false,
        required: true,
      });
      setControlsVisibilityDate();
    }
  }
}

function setControlforProps() {
debugger
  var TimeControl = EdocsApi.getControlProperties("Time1");
  var Reg = EdocsApi.getControlProperties("Reg");
  var Status = EdocsApi.getControlProperties("Status");
  var Link = EdocsApi.getControlProperties("Link");
  var condition = false;
  debugger;
  var Responsible = EdocsApi.getAttributeValue("Responsible")?.value;
  var stage = EdocsApi.getCaseTaskDataByCode(
    "SendEmail" + (Responsible ? JSON.parse(Responsible)[0]?.employeeId : "")
  );
  if (!stage) stage = EdocsApi.getCaseTaskDataByCode("SendEmail");

  if (stage) {
    condition =
      stage.state == "assigned" ||
      stage.state == "inProgress" ||
      stage.state == "completed" ||
      stage.state == "delegated";
  }

  TimeControl.hidden = !condition;
  TimeControl.required = condition;
  EdocsApi.setControlProperties(TimeControl);

  Reg.hidden = !condition;
  EdocsApi.setControlProperties(Reg);

  Status.hidden = !condition;
  Status.required = condition;
  EdocsApi.setControlProperties(Status);

  Link.hidden = !condition;
  Link.required = condition;
  EdocsApi.setControlProperties(Link);
}
function onBeforeCardSave() {
  setEmployeeUnit();
  //setControlforProps();
  //setControlforPropsSendEmail2();
  setControlforSecretary();
  //onUnitMails();
}

function changeVisibleDirector(isVisible) {
  debugger;
  var isContralHidden = !isVisible;
  EdocsApi.setControlProperties({
    code: "Director",
    hidden: isContralHidden,
    required: true,
  });
  EdocsApi.setControlProperties({
    code: "Signatories1",
    hidden: isContralHidden,
    required: false,
  });
  if (isContralHidden) {
    EdocsApi.setAttributeValue({ code: "Signatories1", value: null });
    EdocsApi.setAttributeValue({ code: "Director", value: null });
  }
}

function ldsSave(
  to,
  RegNumber,
  m,
  Description1,
  DOCTITLE,
  Level,
  Levell,
  Details,
  Details2,
  explan,
  Reason,
  info,
  KeyAmend,
  Link,
  persons,
  СontactPerson,
  Time1
) {
  var answer = `<table style="margin-top:-30px;">
        <tr>
            <td>
                <span>Шановні колеги,</span>
<span> </span>
<span>Повідомляємо вам про зареєстрований внутрішній документ <b> " ${Description1} "</b>.</span>
<span> </span>
<span><b>Рівень затвердження: </b>"  ${Level}  ".</span>
<span><b>Дата набуття чинності: </b>" ${Time1} ".</span>
<span><b>Обгрунтування видання документу: </b>" ${explan} ".</span>
<span> </span>
                <span><b>Ключові зміни:</b></span>
           <span>&nbsp;${info}</span>
            </td>
        </tr>
        <tr>
            <td>
                <span>Відповідний документ ви можете знайти на внутрішньому порталі Confluence – " ${Link} ".</span>
            <span> </span>
                <span>Якщо у вас виникнуть запитання щодо вищезазначеного документу, будь ласка, звертайтеся до: <b>${persons}</b></span>
<span> </span>
<span> </span>
                З повагою,
            <span> </span>
               Відділ управління організаційними змінами та проектами
            
               <span>Департаменту управління персоналом та організаційними змінами.</span>
            </td>
        </tr>
        <tr>
            <td >
                ***************************************************
     </td>
        </tr>
    </table>
    <table >
        <tr height="10">
            <td>
                <span>Dear all,</span>
          <span> </span>
                <span>We would like to inform you about the  registered internal regulation <b> " ${DOCTITLE} "</b>.</span>
          <span> </span>
                <span><b>Approval level:</b> " ${Levell} ".</span>
                <span><b>Effective Date:</b>" ${Time1} ".</span>
                <span><b>Reason for the document issuing: </b> " ${Reason} ".</span>
         <span> </span>
                <span>Key amendments:</span>
                &nbsp;${KeyAmend}
            </td>
        </tr>
        <tr>
            <td>
                <span>You may find the respective document on internal portal Confluence – " ${Link} ".</span>
        <span> </span>
                <span>Should you have any questions with regard to the above document please don’t hesitate to contact: <b>${СontactPerson}</b></span> 
<span> </span>
<span> </span>
                <span>Regards,</span>
            <span> </span>
                <span>Organization & Project Management Office</span>
            
                <span>HR & Organization Department.</span>
            </td>
        </tr>
        <tr height="10">
            <td></td>
        </tr>
    </table>`;

  debugger;
  var subject =
    Description1 + " №" + RegNumber.value + "  від " + m.format("DD.MM.YYYY");
  var doc = {
    recipients: to,
    subject: subject,
  };
  answer = answer.trim();
  var bodyAttr = answer;
  if (bodyAttr) {
    doc.body = bodyAttr;
  }

  debugger;
  EdocsApi.sendMsgMail(doc);

  EdocsApi.message("Повідомлення відправлено ");
}

function onTaskExecuteSendEmail(routeStage) {
  if (routeStage.executionResult != "rejected") {
    if (CurrentDocument.isDraft) {
      throw "Прохання зафіксувати версію документа";
    } else {
      var Registr = EdocsApi.getAttributeValue("RegState");
      if (Registr.value != "Зареєстрований") {
        throw "Спочатку зареєструйте документ, а потім виконуйте завдання!";
      } else {
        saveJson();
      }
    }
  }
}

function saveJson() {
  debugger;
  var RegNumber = EdocsApi.getAttributeValue("RegNumber");
  var RegDate = EdocsApi.getAttributeValue("RegDate");
  var m = moment(RegDate.value);
  var Descript = EdocsApi.getAttributeValue("Description1").value;
  var Doctitle = EdocsApi.getAttributeValue("DOCTITLE").value;
  var Level = EdocsApi.getAttributeValue("Level").text;
  var Levell = EdocsApi.getAttributeValue("Levell").text;
  var Details = EdocsApi.getAttributeValue("Details").value;
  var Details2 = EdocsApi.getAttributeValue("Details2").value;
  var Link = EdocsApi.getAttributeValue("Link").value;
  var explan = EdocsApi.getAttributeValue("explan").value;
  var Reason = EdocsApi.getAttributeValue("Reason").value;
  var infoAttr = EdocsApi.getAttributeValue("info");
  var KeyAmendAttr = EdocsApi.getAttributeValue("KeyAmend");
  var personAttr = EdocsApi.getAttributeValue("Person");
  var personEn = EdocsApi.getAttributeValue("СontactPerson").value;

  var timeAttr = EdocsApi.getAttributeValue("Time1");
  var timeValue = moment(timeAttr.value);

  var person = [];

  var info = [];
  var infoEn = [];

  var Emails = getEmails();

  if (infoAttr && infoAttr.value) {
    var arrInfo = infoAttr.value.split("\n");
    for (var i = 0; i < arrInfo.length; i++) {
      info.push("<li>" + arrInfo[i] + "</li>");
    }
  }

  if (KeyAmendAttr && KeyAmendAttr.value) {
    var arrInfo = KeyAmendAttr.value.split("\n");
    for (var i = 0; i < arrInfo.length; i++) {
      infoEn.push("<li>" + arrInfo[i] + "</li>");
    }
  }

  if (personAttr && personAttr.value) {
    var employees = JSON.parse(personAttr.value);

    for (var i = 0; i < employees.length; i++) {
      var employee = EdocsApi.getEmployeeDataByEmployeeID(
        employees[i].employeeId
      );
      if (employee.email) {
        person.push(
          "<li><span>" +
            employee.shortName +
            '</span>&nbsp;<a href = "mailto: ' +
            employee.email +
            '">(' +
            employee.email +
            ")</a></li>"
        );
      } else {
        person.push("<li><span>" + employee.shortName + "</span></li>");
      }
    }
  }

  var persons = person.join(", ");

  ldsSave(
    Emails,
    RegNumber,
    m,
    Descript,
    Doctitle,
    Level,
    Levell,
    Details,
    Details2,
    explan,
    Reason,
    info.join("").replace("\n", ""),
    infoEn.join("").replace("\n", ""),
    Link,
    persons,
    personEn,
    timeValue.format("DD.MM.YYYY")
  );
}
function onChangeunitmails() {
  debugger;
  //var mails = getEmails();
  setUnitList();
}

function getEmails() {
  var unitmailsAttr = EdocsApi.getAttributeValue("unitmails");
  if (unitmailsAttr && unitmailsAttr.value) {
    var arr = JSON.parse(unitmailsAttr.value);
    var unitmails = arr.map(function (item) {
      debugger;
      var attributes = EdocsApi.getDictionaryItemData(
        item.dictionaryCode,
        item.id
      ).attributes;
      var mails = EdocsApi.findElementByProperty(
        "code",
        "_x0076_qv9",
        attributes
      ).value;
      return mails;
    });

    var mails = unitmails.join(",");
    return mails.split(",");
  }

  return [];
}

function onChangeEmployee() {
  var emp = EdocsApi.getAttributeValue("initiator");
  var headAttr = { code: "HeadEmployee", value: null };
  var cCodeAttr = { code: "cCode", value: null };
  var positionAttr = { code: "position", value: null };
  var unit1Attr = { code: "Unit1", value: null };
  var loginAttr = EdocsApi.getAttributeValue("login");
  loginAttr.value = null;
  if (emp.value) {
    var employeeData = EdocsApi.getEmployeeDataByEmployeeID(emp.value);
    var unitLevel = employeeData.unitLevel;
    loginAttr.value = employeeData.employeeLogin;
    while (unitLevel >= 0) {
      try {
        var head = EdocsApi.getEmployeeManagerByEmployeeID(
          emp.value,
          unitLevel
        );
        if (head && (head.employeeId != emp.value || unitLevel === 0)) {
          headAttr.value = head.employeeId;
          cCodeAttr.value = head.unitCode;
          break;
        }
      } catch {}
      unitLevel--;
    }
  }
}

function onSearchLevell(request) {
  var Type = EdocsApi.getAttributeValue("Type");
  request.filterCollection.push({ attributeCode: "Title", value: Type.text });
}
function onSearchLevel(request) {
  var Type = EdocsApi.getAttributeValue("Type");
  request.filterCollection.push({ attributeCode: "Title", value: Type.text });
}

function onDisbleStatus() {
  var Person = CurrentUser.employeeId;
  var UnitEmployee = EdocsApi.findEmployeeSubdivisionByLevelAndEmployeeID(
    Person,
    0
  );
  var Status = EdocsApi.getControlProperties("Status");

  if (UnitEmployee) {
    if (UnitEmployee.unitCode == "21268") {
      Status.disabled = false;
    } else {
      Status.disabled = true;
    }
  }
  EdocsApi.setControlProperties(Status);
}

function setEmployeeUnit() {
  var employeeUnit = EdocsApi.getAttributeValue("EmployeeUnit");
  if (employeeUnit) {
    if (!employeeUnit.value) {
      var UnitEmployee = EdocsApi.findEmployeeSubdivisionByLevelAndEmployeeID(
        CurrentUser.employeeId,
        2
      );
      employeeUnit.text = UnitEmployee.unitName;
      employeeUnit.value = UnitEmployee.unitId;
      EdocsApi.setAttributeValue(employeeUnit);
      EdocsApi.setControlProperties({
        code: "EmployeeUnit",
        hidden: false,
        required: false,
      });
    }
  }
}
function checkVisibleEmployeeUnit() {
  var employeeUnit = EdocsApi.getAttributeValue("EmployeeUnit");
  if (employeeUnit) {
    if (!employeeUnit.value) {
      EdocsApi.setControlProperties({
        code: "EmployeeUnit",
        hidden: true,
        required: false,
      });
    }
  }
}

function onChangePerson() {
  var atribute = { code: "СontactPerson" };
  var person = EdocsApi.getAttributeValue("Person");
  if (person && person.value) {
    var eployeesEn = [];
    var employees = JSON.parse(person.value);
    for (var i = 0; i < employees.length; i++) {
      var emploee = EdocsApi.getEmployeeDataByEmployeeID(
        employees[i].employeeId
      );
      eployeesEn.push(emploee.phone3);
    }

    eployeesEn = eployeesEn.filter(function (item) {
      return item && item.length > 0;
    });
    if (eployeesEn.length > 0) {
      atribute.value = eployeesEn.join(", ");
    }
  }
  EdocsApi.setAttributeValue(atribute);
}

function onChangeConf() {
  var conf = EdocsApi.getAttributeValue("Conf");
  var conf2 = EdocsApi.getAttributeValue("Conf2");
  if (conf.value) {
    var dic = EdocsApi.getDictionaryItemData("Security1", conf.value);

    var text = EdocsApi.findElementByProperty(
      "code",
      "b6ug",
      dic.attributes
    ).value;
    conf2.value = text;
    EdocsApi.setAttributeValue(conf2);
  }
}

function onChangeDirector() {
  var atribute = { code: "Signatories1" };
  var person = EdocsApi.getAttributeValue("Director");
  if (person && person.value) {
    var eployeesEn = [];
    var employees = JSON.parse(person.value);
    for (var i = 0; i < employees.length; i++) {
      var emploee = EdocsApi.getEmployeeDataByEmployeeID(
        employees[i].employeeId
      );
      eployeesEn.push(emploee.phone3);
    }
    eployeesEn = eployeesEn.filter(function (item) {
      return item && item.length > 0;
    });
    if (eployeesEn.length > 0) {
      atribute.value = eployeesEn.join(", ");
    }
  }
  EdocsApi.setAttributeValue(atribute);
}
function onChangetoAttention1() {
  var toAttention1 = EdocsApi.getAttributeValue("toAttention1");
  changeVisibleControl("Familiarization1", toAttention1.value == "true", false);
}
function onChangetoAttention() {
  var toAttention = EdocsApi.getAttributeValue("toAttention");
  changeVisibleControl("Familiarization", toAttention.value == "true", false);
}

function changeVisibleControl(attributeCode, isVisible, required) {
  var isContralHidden = !isVisible;
  EdocsApi.setControlProperties({
    code: attributeCode,
    hidden: isContralHidden,
    required: required,
  });
  if (isContralHidden) {
    EdocsApi.setAttributeValue({ code: attributeCode, value: null });
    EdocsApi.setAttributeValue({ code: attributeCode, value: null });
  }
}
function setUnitList() {
  debugger;
  var unitmailsAttr = EdocsApi.getAttributeValue("unitmails").value;
  if (unitmailsAttr) {
    var unitmails = JSON.parse(unitmailsAttr);
    var unitList = { code: "UnitList", value: "", text: "" };
    var list = [];
    var text = "";
    debugger;
    for (var i = 0; i < unitmails.length; i++) {
      var id = unitmails[i].id;
      var dic = EdocsApi.getDictionaryItemData("unitmails", id);
      var name = EdocsApi.findElementByProperty(
        "code",
        "ea3g",
        dic.attributes
      ).value;
      text = text + (text.length > 0 ? "\n" : "") + name;
      var obj = {
        value: id,
        text: name,
        itemCode: null,
        itemDictionary: "unitmails",
        index: i,
      };
      list.push(obj);
    }

    unitList.text = text;
    unitList.value = JSON.stringify(list);
    EdocsApi.setAttributeValue(unitList);
    //	EdocsApi.setAttributeValue(unitmails);
    debugger;
  }
}
//відображення полів залежно від дати створення документа
function setControlsVisibilityDate() {
  debugger;
  if (new Date(CurrentDocument.created) > new Date("2022-10-25")) {
    changeControlPropertiesHide("ResponsibleOld");
    changeControlPropertiesShow("Responsible");
  } else {
    changeControlPropertiesHide("Responsible");
    changeControlPropertiesShow("ResponsibleOld");
  }
}
function changeControlPropertiesHide(code) {
  debugger;
  var controle = EdocsApi.getControlProperties(code);
  controle.hidden = true;
  EdocsApi.setControlProperties(controle);
}
function changeControlPropertiesShow(code) {
  var controle = EdocsApi.getControlProperties(code);
  controle.hidden = false;
  EdocsApi.setControlProperties(controle);
}

function setControlReg() {
  debugger;
  const Responsible = EdocsApi.getAttributeValue("Responsible").value;
  if (Responsible) {
    const TaskReg = EdocsApi.getCaseTaskDataByCode(
      "TaskReg" + JSON.parse(Responsible)[0].employeeId
    );
    const SendEmail = EdocsApi.getCaseTaskDataByCode(
      "SendEmail" + JSON.parse(Responsible)[0].employeeId
    );
    if (
      (TaskReg &&
        (TaskReg.state == "inProgress" || TaskReg.state == "completed")) ||
      (SendEmail &&
        (SendEmail.state == "inProgress" || SendEmail.state == "completed"))
    ) {
      changeControlPropertiesShow("Reg");
    } else {
      changeControlPropertiesHide("Reg");
    }
  } else {
    changeControlPropertiesHide("Reg");
  }
}

function onTaskExecuteSendEmail2(routeStage) {
  if (routeStage.executionResult == "executed") {
    saveJson();
  }
}

function onTaskExecuteTaskReg(routeStage) {
  debugger;

  if (routeStage.executionResult != "rejected") {
    var Registr = EdocsApi.getAttributeValue("RegState");

    if (Registr.value != "Зареєстрований") {
      throw "Спочатку зареєструйте документ, а потім виконуйте завдання!";
    }

    if (CurrentDocument.isDraft) {
      throw "Прохання зафіксувати версію документа";
    }
  }
}
/*
function setControlforPropsSendEmail2() {
  var TimeControl = EdocsApi.getControlProperties("Time1");
  var Status = EdocsApi.getControlProperties("Status");
  var Link = EdocsApi.getControlProperties("Link");
  var condition = false;
  debugger;
  var Responsible = EdocsApi.getAttributeValue("Responsible")?.value;
  var stage = EdocsApi.getCaseTaskDataByCode(
    "SendEmail2" + (Responsible ? JSON.parse(Responsible)[0]?.employeeId : "")
  );
  if (!stage) stage = EdocsApi.getCaseTaskDataByCode("SendEmail2");

  if (stage) {
    condition =
      stage.state == "assigned" ||
      stage.state == "inProgress" ||
      stage.state == "completed" ||
      stage.state == "delegated";
  }

  TimeControl.hidden = !condition;
  TimeControl.required = condition;
  EdocsApi.setControlProperties(TimeControl);

  Status.hidden = !condition;
  Status.required = condition;
  EdocsApi.setControlProperties(Status);

  Link.hidden = !condition;
  Link.required = condition;
  EdocsApi.setControlProperties(Link);
}
*/

function setPropDirector() {
  if (EdocsApi.getAttributeValue("Director").value) {
    changeControlPropertiesShow("Director");
  } else {
    changeControlPropertiesHide("Director");
  }
}

function setPropSignatories1() {
  if (EdocsApi.getAttributeValue("Signatories1").value) {
    changeControlPropertiesShow("Signatories1");
  } else {
    changeControlPropertiesHide("Signatories1");
  }

}
function setPropertyRequired(attributeName, boolValue = true) {

  //обов"язкове

  var attributeProps = EdocsApi.getControlProperties(attributeName);

  attributeProps.required = boolValue;

  EdocsApi.setControlProperties(attributeProps);

}

 

function setControlRegVisible() {
  debugger;
  const Responsible = EdocsApi.getAttributeValue("Responsible").value;

  if ( Responsible &&
      (EdocsApi.getCaseTaskDataByCode(
        "SendEmail2" + JSON.parse(Responsible)[0].employeeId
      )?.state == "inProgress"  ||
      EdocsApi.getCaseTaskDataByCode(
        "SendEmail2" + JSON.parse(Responsible)[0].employeeId
      )?.state == "completed")) {

    changeControlPropertiesShow("Reg");
   changeControlPropertiesShow("Time1");
   changeControlPropertiesShow("Link");
    setPropertyRequired("Time1");
    setPropertyRequired("Link");

  } 
//else {

//    changeControlPropertiesHide("Reg");

 //   setPropertyRequired("Time1", false);

//    setPropertyRequired("Link", false);

//  }

}