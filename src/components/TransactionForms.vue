<template>
    <!-- Create Transaction -->
	<div class="form_container" v-if="transform == `createTransaction`">
		<div class="form">
			<label for="create_trans_date">Transaction Date:</label>
			<input id="create_trans_date" type="date" />

			<label for="create_trans_account">Account:</label>
			<select id="create_trans_account">
				<option v-for="account in userObj['records']['accounts']" :key="account" :data="account">
					{{ account }}
				</option>
			</select>
			
			<label for="create_trans_payee">Payee:</label>
			<v-select 
				:options="userObj['records']['payee']" 
				style="width: 200px" 
				id="create_trans_payee"
				:value="``"
			/>

			<label for="create_trans_type">Type:</label>
			<select id="create_trans_type">
				<option value="Credit">Credit</option>
				<option value="Debit">Debit</option>
			</select>

			<label for="create_trans_category">Category:</label>
			<select id="create_trans_category">
				<option v-for="(status, category) in userObj['records']['categories']" :key="category" :data="category">
					{{ category }}
				</option>
			</select>
			
			<label for="create_trans_item">Item:</label>
			<input id="create_trans_item" type="text" />

			<label for="create_trans_amount">Amount:</label>
			<input id="create_trans_amount" type="number" step="0.01" />
			<template v-if="fileUploaded === false">
				<q-btn class="glossy" rounded color="positive" label="Upload Receipt" @click="getReceiptPath"/>
			</template>
			<template v-else>
				Your receipt has been uploaded
			</template>
			<fieldset>
				<q-btn class="glossy" rounded color="primary" label="Create Transaction" @click="createTransaction"/>
                <q-btn class="glossy" rounded color="secondary" label="Cancel" @click="cancel"/>
			</fieldset>
		</div>
	</div>
	
	<!-- Edit Transaction -->
	<div class="form_container" v-if="transform == `editTransaction`">
		<div class="form">
			<div id="edit_transID" transid="invalid" transyear="invalid"></div>
			<label for="edit_trans_date">Transaction Date:</label>
			<input id="edit_trans_date" type="date" />

			<label for="edit_trans_account">Account:</label>
			<select id="edit_trans_account">
				<option v-for="account in userObj['records']['accounts']" :key="account" :data="account">
					{{ account }}
				</option>
			</select>

			<label for="edit_trans_payee">Payee:</label>
			<v-select :options="userObj['records']['payee']" style="width: 200px" id="edit_trans_payee"></v-select>

			<label for="edit_trans_type">Type:</label>
			<select id="edit_trans_type">
				<option value="Credit">Credit</option>
				<option value="Debit">Debit</option>
			</select>

			<label for="edit_trans_category">Category:</label>
			<select id="edit_trans_category">
				<option v-for="(status, category) in userObj['records']['categories']" :key="category" :data="category">
					{{ category }}
				</option>
			</select>
			
			<label for="edit_trans_item">Item:</label>
			<input id="edit_trans_item" type="text" />

			<label for="edit_trans_amount">Amount:</label>
			<input id="edit_trans_amount" type="number" step="0.01" />

            <template v-if="hasReceipt === false">
				<q-btn class="glossy" rounded color="positive" label="Upload Receipt" @click="getReceiptPath"/>
			</template>
			<template v-else>
				This transaction has a receipt
			</template>

			<fieldset>
				<q-btn class="glossy" rounded color="primary" label="Save Transaction" @click="editTransaction"/>
                <q-btn class="glossy" rounded color="red" label="Delete" @click="deleteTransaction"/>
                <q-btn class="glossy" rounded color="secondary" label="Cancel" @click="this.$emit('cancelled', '')"/>
			</fieldset>
		</div>
	</div>

    <!-- Add Saved Transactions -->
	<div class="form_container" v-if="transform == `addSaved`">
		<div class="form">
            <div id="add_savedID" savedid="invalid"></div>

			<label for="add_saved_date">Date:</label>
			<input id="add_saved_date" type="date" />

			<fieldset>
                <q-btn class="glossy" rounded color="primary" label="Add Saved Transaction" @click="addSaved"/>
                <q-btn class="glossy" rounded color="secondary" label="Cancel" @click="this.$emit('cancelled', '')"/>
			</fieldset>
		</div>
	</div>

    <!-- Create Saved Transactions -->
	<div class="form_container" v-if="transform == `createSaved`">
		<div class="form">
			<label for="create_savedTrans_account">Account:</label>
			<select id="create_savedTrans_account">
				<option v-for="account in userObj['records']['accounts']" :key="account" :data="account">
					{{ account }}
				</option>
			</select>

			<label for="create_savedTrans_type">Type:</label>
			<select id="create_savedTrans_type">
				<option value="Credit">Credit</option>
				<option value="Debit">Debit</option>
			</select>

			<label for="create_savedTrans_category">Category:</label>
			<select id="create_savedTrans_category">
				<option v-for="(status, category) in userObj['records']['categories']" :key="category" :data="category">
					{{ category }}
				</option>
			</select>
			
			<label for="create_savedTrans_item">Item:</label>
			<input id="create_savedTrans_item" type="text" />

			<label for="create_savedTrans_amount">Amount:</label>
			<input id="create_savedTrans_amount" type="number" step="0.01" />

			<fieldset>
                <q-btn class="glossy" rounded color="primary" label="Create Saved Transaction" @click="createSaved"/>
                <q-btn class="glossy" rounded color="secondary" label="Cancel" @click="this.$emit('cancelled', '')"/>
			</fieldset>
		</div>
	</div>

    <!-- Edit Saved Transactions -->
	<div class="form_container" v-if="transform == `editSaved`">
		<div class="form">
            <div id="edit_savedID" savedid="invalid"></div>
			<label for="edit_savedTrans_account">Account:</label>
			<select id="edit_savedTrans_account">
				<option v-for="account in userObj['records']['accounts']" :key="account" :data="account">
					{{ account }}
				</option>
			</select>

			<label for="edit_savedTrans_type">Type:</label>
			<select id="edit_savedTrans_type">
				<option value="Credit">Credit</option>
				<option value="Debit">Debit</option>
			</select>

			<label for="edit_savedTrans_category">Category:</label>
			<select id="edit_savedTrans_category">
				<option v-for="(status, category) in userObj['records']['categories']" :key="category" :data="category">
					{{ category }}
				</option>
			</select>
			
			<label for="edit_savedTrans_item">Item:</label>
			<input id="edit_savedTrans_item" type="text" />

			<label for="edit_savedTrans_amount">Amount:</label>
			<input id="edit_savedTrans_amount" type="number" step="0.01" />

			<fieldset>
                <q-btn class="glossy" rounded color="primary" label="Save Transaction" @click="editSaved"/>
                <q-btn class="glossy" rounded color="red" label="Delete" @click="deleteSaved"/>
                <q-btn class="glossy" rounded color="secondary" label="Cancel" @click="this.$emit('cancelled', '')"/>
			</fieldset>
		</div>
	</div>

    <!-- Create Asset -->
	<div class="form_container" v-if="transform == `createAsset`">
		<div class="form">
			<label for="create_asset_date">Purchased Date:</label>
			<input id="create_asset_date" type="date" />

			<label for="create_asset_item">Item Description:</label>
			<input id="create_asset_item" type="text" />

			<label for="create_asset_vendor">Vendor:</label>
			<input id="create_asset_vendor" type="text" />

			<label for="create_asset_unit_cost">Unit Cost:</label>
			<input id="create_asset_unit_cost" type="number" step="0.01" />

			<label for="create_asset_units">Units:</label>
			<input id="create_asset_units" type="number" step="0.01" />

			<label for="create_asset_total">Total:</label>
			<input id="create_asset_total" type="number" step="0.01" />

			<fieldset>
                <q-btn class="glossy" rounded color="primary" label="Create Asset" @click="createAsset"/>
                <q-btn class="glossy" rounded color="secondary" label="Cancel" @click="this.$emit('cancelled', '')"/>
			</fieldset>
		</div>
	</div>

	<!-- Edit Asset -->
	<div class="form_container" v-if="transform == `editAsset`">
		<div class="form">
			<div id="edit_assetID" assetid="invalid" assetyear="invalid"></div>

			<label for="edit_asset_date">Purchased Date:</label>
			<input id="edit_asset_date" type="date" />

			<label for="edit_asset_item">Item Description:</label>
			<input id="edit_asset_item" type="text" />

			<label for="edit_asset_vendor">Vendor:</label>
			<input id="edit_asset_vendor" type="text" />

			<label for="edit_asset_unit_cost">Unit Cost:</label>
			<input id="edit_asset_unit_cost" type="number" step="0.01" />

			<label for="edit_asset_units">Units:</label>
			<input id="edit_asset_units" type="number" step="0.01" />

			<label for="edit_asset_total">Total:</label>
			<input id="edit_asset_total" type="number" step="0.01" />

			<fieldset>
                <q-btn class="glossy" rounded color="primary" label="Save Asset" @click="editAsset"/>
                <q-btn class="glossy" rounded color="red" label="Delete" @click="deleteAsset"/>
                <q-btn class="glossy" rounded color="secondary" label="Cancel" @click="this.$emit('cancelled', '')"/>
			</fieldset>
		</div>
	</div>


</template>

<script>
import { userDict, settingsDict } from '../main.js'
import { generateID, reDoDate, addToDate } from '../../public/generalFunctions.js';
import $ from 'jquery';
import vSelect from 'vue-select';
import 'vue-select/dist/vue-select.css';
const dialog = window.__TAURI__.dialog;
const fs = window.__TAURI__.fs;
const path = window.__TAURI__.path;
export default {
    name: 'RecordForms',
    emits: ["cancelled"],
    components: {
		vSelect
	},
    props: {
        transform: String,
        hasReceipt: Boolean
    },
    data() {
        return {
            userObj: userDict,
            fileUploaded: false,
            filePath: '',
        }
    },
    methods: {
        getReceiptPath(){
            let ref = this;
            dialog.open().then(function(file) {
                ref.filePath = file;
                ref.fileUploaded = true;
            });
        },
        createTransaction(){
			const monthNames = ["Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"];
			let date = ($('#create_trans_date').val()).split("-");
			let month = parseInt(date[1]) - 1;
			let thisYear = parseInt(date[0]);
			date = date.reverse().join("/");
            if(date == '' || date == 'NaN/NaN/NaN'){ //If no date was provided
				$("#create_trans_date").addClass('form_error');
				return false;
			}
            $("#create_trans_date").removeClass('form_error');

			let account = $(`#create_trans_account option:selected`).val();
			let type = $(`#create_trans_type option:selected`).val();
			let category = $(`#create_trans_category option:selected`).val();
			let payee = $(`.vs__selected`).text();
			let item = $('#create_trans_item').val();
			let amount = type == 'Credit' ? Math.abs(parseFloat($('#create_trans_amount').val())) : 0 - parseFloat($('#create_trans_amount').val());

			let yearID;
			if(month < 3){
				yearID = `${thisYear - 1} - ${thisYear}`;
			}else{
				yearID = `${thisYear} - ${thisYear + 1}`;
			}

			const transID = generateID(userDict);
			if(!Object.keys(userDict['records']['years']).includes(yearID)){
				userDict['records']['years'][yearID] = {'transactions': {}, 'assets': {}};
			}

            let fileName = '';
            let ref = this;
            if(this.fileUploaded){
                const receiptID = generateID(userDict);
                fileName = `${receiptID}-${ref.filePath.split('\\').at(-1)}`;
                fs.copyFile(ref.filePath, settingsDict['roaming'] + `DashBooks/Receipts/${fileName}`)
            }

			userDict['records']['years'][yearID]['transactions'][transID] = {'month': monthNames[month], 'date': date, 'account': account, 'payee': payee, 'type': type, 'item': item, 'category': category, 'amount': amount, 'receiptID': fileName, 'id': transID}
            this.fileUploaded = false;
			this.$emit('cancelled', '');
		},
		editTransaction(){
			const monthNames = ["Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"];
			this.recordDict = userDict['records']['years'][$('#edit_transID').attr('transyear')];
			const ID = $('#edit_transID').attr('transid');
            let date = ($('#edit_trans_date').val()).split("-").reverse().join("/");

            if(date == '' || date == 'NaN/NaN/NaN'){ //If no date was provided
				$("#edit_trans_date").addClass('form_error');
				return false;
			}
            $("#edit_trans_date").removeClass('form_error');
			let month = parseInt(date.split('/')[1]) - 1;
			let year = parseInt(date.split('/')[2]);

			let account = $(`#edit_trans_account option:selected`).val();
			let type = $(`#edit_trans_type option:selected`).val();
			let payee = $(`.vs__selected`).text();
			let category = $(`#edit_trans_category option:selected`).val();
			let item = $('#edit_trans_item').val();

			let preAmount = Math.abs(parseFloat($('#edit_trans_amount').val()))
			let amount = type == 'Credit' ? preAmount : 0 - preAmount;
			
			let yearID;
			if(month < 3){
				yearID = `${year - 1} - ${year}`;
			}else{
				yearID = `${year} - ${year + 1}`;
			}

            let fileName = '';
            let ref = this;
            if(this.fileUploaded){
                const receiptID = generateID(userDict);
                fileName = `${receiptID}-${ref.filePath.split('\\').at(-1)}`;
                fs.copyFile(ref.filePath, settingsDict['roaming'] + `DashBooks/Receipts/${fileName}`)
            }

			delete this.recordDict['transactions'][ID]
			userDict['records']['years'][yearID]['transactions'][ID] = {'month': monthNames[month], 'date': date, 'account': account, 'payee': payee, 'type': type, 'item': item, 'category': category, 'amount': amount, 'id': ID, 'receiptID': fileName}
			this.$emit('cancelled', '');
            this.fileUploaded = false;
		},
		deleteTransaction(){
			const ID = $('#edit_transID').attr('transid');
			const YEAR = $('#edit_transID').attr('transyear');

            let ref = this;
            confirm(`Are you sure you want to delete this transaction? Note: This won't delete the receipt.`).then(function(outcome) {
                if(outcome){
                    delete userDict['records']['years'][YEAR]['transactions'][ID];
                }
                ref.$emit('cancelled', '');
            });
		},
		createSaved(){
            const savedID = generateID(userDict);
			let account = $(`#create_savedTrans_account option:selected`).val();
			let type = $(`#create_savedTrans_type option:selected`).val();
			let category = $(`#create_savedTrans_category option:selected`).val();
			let item = $('#create_savedTrans_item').val();
			let amount = type == 'Credit' ? Math.abs(parseFloat($('#create_savedTrans_amount').val())) : 0 - Math.abs(parseFloat($('#create_savedTrans_amount').val()));

            userDict['records']['savedTransactions'][savedID] = {'account': account, 'type': type, 'item': item, 'category': category, 'amount': amount};
			this.$emit('cancelled', '');
		},
        editSaved(){
            const savedID = $('#edit_savedID').attr('savedid');
            let account = $(`#edit_savedTrans_account option:selected`).val();
			let type = $(`#edit_savedTrans_type option:selected`).val();
			let category = $(`#edit_savedTrans_category option:selected`).val();
			let item = $('#edit_savedTrans_item').val();
			let amount = type == 'Credit' ? Math.abs(parseFloat($('#edit_savedTrans_amount').val())) : 0 - Math.abs(parseFloat($('#edit_savedTrans_amount').val()));

            userDict['records']['savedTransactions'][savedID] = {'account': account, 'type': type, 'item': item, 'category': category, 'amount': amount};
			this.$emit('cancelled', '');
        },
        deleteSaved(){
            const savedID = $('#edit_savedID').attr('savedid');
            let ref = this;
            confirm(`Are you sure you want to delete this saved Transaction?`).then(function(outcome) {
                if(outcome){
                    delete userDict['records']['savedTransactions'][savedID]
                }
                ref.$emit('cancelled', '');
            });
        },
        addSaved(){
            const monthNames = ["Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"];
            const savedID = $('#add_savedID').attr('savedid')
			let date = ($('#add_saved_date').val()).split("-");
			let month = parseInt(date[1]) - 1;
			let thisYear = parseInt(date[0]);
			date = date.reverse().join("/");
            if(date == '' || date == 'NaN/NaN/NaN'){ //If no date was provided
				$("#add_saved_date").addClass('form_error');
				return false;
			}
            $("#add_saved_date").removeClass('form_error');

            let account = userDict['records']['savedTransactions'][savedID]['account']
			let type = userDict['records']['savedTransactions'][savedID]['type']
			let category = userDict['records']['savedTransactions'][savedID]['category']
			let item = userDict['records']['savedTransactions'][savedID]['item']
			let amount = userDict['records']['savedTransactions'][savedID]['amount']

			let yearID;
			if(month < 3){
				yearID = `${thisYear - 1} - ${thisYear}`;
			}else{
				yearID = `${thisYear} - ${thisYear + 1}`;
			}

			const transID = generateID(userDict);
			if(!Object.keys(userDict['records']['years']).includes(yearID)){
				userDict['records']['years'][yearID] = {'transactions': {}, 'assets': {}};
			}
			userDict['records']['years'][yearID]['transactions'][transID] = {'month': monthNames[month], 'date': date, 'account': account, 'type': type, 'item': item, 'category': category, 'amount': amount, 'receiptID': '', 'id': transID}

			this.$emit('cancelled', '');
        },
        createAsset(){
			let item = $('#create_asset_item').val();
			let vendor = $('#create_asset_vendor').val();
			let date = ($('#create_asset_date').val()).split("-");
			let month = parseInt(date[1]) - 1;
			let thisYear = parseInt(date[0]);
			date = date.reverse().join("/");
            if(date == '' || date == 'NaN/NaN/NaN'){ //If no date was provided
				$("#create_asset_date").addClass('form_error');
				return false;
			}
            $("#create_asset_date").removeClass('form_error');
            let yearID;
			if(month < 3){
				yearID = `${thisYear - 1} - ${thisYear}`;
			}else{
				yearID = `${thisYear} - ${thisYear + 1}`;
			}

			if(!Object.keys(userDict['records']['years']).includes(yearID)){
				userDict['records']['years'][yearID] = {'transactions': {}, 'assets': {}};
			}

			let unitCost = parseFloat($(`#create_asset_unit_cost`).val());
			let units = parseFloat($(`#create_asset_units`).val());
			let total = parseFloat($(`#create_asset_total`).val());
            isNaN(unitCost) ? unitCost = 0 : '';
            isNaN(units) ? units = 0 : '';
            isNaN(total) ? total = 0 : '';

			const assetID = generateID(userDict);
			userDict['records']['years'][yearID]['assets'][assetID] = {'item': item, 'vendor': vendor, 'date': date, 'unitCost': unitCost, 'units': units, 'total': total}
			this.$emit('cancelled', '');
		},
        editAsset(){
            let date = ($('#edit_asset_date').val()).split("-");
			let month = parseInt(date[1]) - 1;
			let thisYear = parseInt(date[0]);
			date = date.reverse().join("/");
            if(date == '' || date == 'NaN/NaN/NaN'){ //If no date was provided
				$("#edit_asset_date").addClass('form_error');
				return false;
			}
            $("#edit_asset_date").removeClass('form_error');
            let yearID;
			if(month < 3){
                yearID = `${thisYear - 1} - ${thisYear}`;
			}else{
                yearID = `${thisYear} - ${thisYear + 1}`;
			}

			if(!Object.keys(userDict['records']['years']).includes(yearID)){
                userDict['records']['years'][yearID] = {'transactions': {}, 'assets': {}};
			}

            let item = $('#edit_asset_item').val();
			let vendor = $('#edit_asset_vendor').val();
            let unitCost = parseInt($(`#edit_asset_unit_cost`).val());
			let units = parseInt($(`#edit_asset_units`).val());
			let total = parseInt($(`#edit_asset_total`).val());
            const assetID = $('#edit_assetID').attr('assetid');

            userDict['records']['years'][yearID]['assets'][assetID] = {'item': item, 'vendor': vendor, 'date': date, 'unitCost': unitCost, 'units': units, 'total': total}
			this.$emit('cancelled', '');
		},
		deleteAsset(){
            const ID = $('#edit_assetID').attr('assetid');
			const YEAR = $('#edit_assetID').attr('assetyear');
            let ref = this;
            confirm(`Are you sure you want to delete this Asset?`).then(function(outcome) {
                if(outcome){
                    delete userDict['records']['years'][YEAR]['assets'][ID];
                }
                ref.$emit('cancelled', '');
            });
		},
        cancel(){
            this.$emit('cancelled', '');
            this.fileUploaded = false;
            this.filePath = '';
        }
    }
}
</script>

<style scoped lang="scss">
input {
	width: 200px;
	padding: 2px;
	font-family: 'Segoe UI', sans-serif;
	font-size: 15px;
	background-color: transparent;
	border: 1px solid black;
	border-bottom: 3px solid black;
	border-radius: 5px;
	outline: unset;
	transition: 0.2s ease border;
}

.form_error {
	border-color: red !important;
}

.form_container {
	position: absolute;
	z-index: 600;
	inset: 0px;
	opacity: 1;
	display: flex;
	justify-content: center;
	align-items: center;
	font-family: 'Segoe UI', sans-serif;
	transition: 0.2s ease all;
    backdrop-filter: blur(15px);
}

.form{
	display: flex;
	position: relative;
	flex-direction: column;
	justify-content: center;
	align-items: center;
	gap: 10px;
	padding: 20px;
	background-color: white;
    border: 1px solid $accent;
	border-radius: 5px;
	transition: 0.3s ease all;
}

.form fieldset{
	position: relative;
	display: flex;
	flex-direction: column;
	justify-content: center;
	align-items: center;
	gap: 10px;
	margin-top: 20px;
	border: unset;
}

select{
	margin: 10px 0px;
	border-radius: 10px;
	padding: 5px;
	width: 110px;
    height: 35px;
	outline: none;
}

fieldset > .button_link:nth-last-child(1){
	background-color:#bb1818;
}
</style>
