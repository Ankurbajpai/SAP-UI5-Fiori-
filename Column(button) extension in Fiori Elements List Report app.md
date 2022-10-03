 Add a new column(button) in Fiori Elements List Report via extension.

- Add below code in manifest.json file in extends section.

```json
	"sap.ui.viewExtensions": {
					"sap.suite.ui.generic.template.ListReport.view.ListReport": {
						"ResponsiveTableColumnsExtension|ZPTP_SO10_CDS": {
							"type": "XML",
							"className": "sap.ui.core.Fragment",
							"fragmentName": "app.p2r.zui_ux_p2r_so10.ext.view.ColumnExt"
						},
						"ResponsiveTableCellsExtension|ZPTP_SO10_CDS": {
							"type": "XML",
							"className": "sap.ui.core.Fragment",
							"fragmentName": "app.p2r.zui_ux_p2r_so10.ext.view.CellsExt"
						}
					}
				},
```

- Create two fragments file.
  
  CellsExt.fragment.xml
  
  ```xml
  <core:FragmentDefinition xmlns:core="sap.ui.core" xmlns="sap.m">
  		<Button id="btn" icon="sap-icon://hint" press = "onClickLine" tooltip = "Show Details" />
  </core:FragmentDefinition>
  ```

ColumnExt.fragment.xml

```xml
<core:FragmentDefinition xmlns:core="sap.ui.core" xmlns="sap.m">
	<Column id="ExtensionWizard::ColumnBreakout">
		<Text text="Display Text"/>
		<customData>
			<core:CustomData key="p13nData" value='\{"columnKey": "Test1", "columnIndex" : "70"}'/>
		</customData>
	</Column>
</core:FragmentDefinition>
```

My requirement was to fetch some data from t-code **SO10**  once user click on extended column(button).

ListReportExt.controller.js

```js
sap.ui.controller("app.p2r.zui_ux_p2r_so10.ext.controller.ListReportExt", {

	onClickLine: function (oEvent) {
		var oObject = oEvent.getSource().getBindingContext().getObject();
		var sTdName = oObject.tdname;
		var sTId = oObject.tdid;
		var sLang = oObject.tdspras;
		sTdName = sTdName.replaceAll("/", "*");
		/*	var that = this;
			var filters = [];
			filters.push(new sap.ui.model.Filter([
				new sap.ui.model.Filter("Textname", sap.ui.model.FilterOperator.EQ, sTdName),
				new sap.ui.model.Filter("Textid", sap.ui.model.FilterOperator.EQ, sTId),
				new sap.ui.model.Filter("Textlang", sap.ui.model.FilterOperator.EQ, sLang)
			], true));*/
		var oModel = this.getOwnerComponent().getModel("readText");

		oModel.read("/ZPTP_SO10_RDTEXTSet(Textname='" + sTdName + "',Textid='" + sTId + "',Textlang='" +
			sLang + "')", {
				success: function (oData, oResponse) {
					sap.m.MessageBox.information(oData.TextLines, {
						title: "Display iShift PO Text: " + sTdName + " Language " + sLang,
						id: "messageBoxId1",
						contentWidth: "800px"
					});
				},
				error: function (oError) {
					sap.m.MessageBox.error(
						"There is no details found for selected record."
					);
				}
			});
	}
});
```
