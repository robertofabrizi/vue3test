[11:11] PIROZZI MATTEO (GUEST)
<template>
 
    <div>
 
 
        <!-- SEZIONE PRINCIPALE -->
        <div v-if="this.getAppRitiri.ricercaEseguita">
 
            <div style="display:flex;">
 
                <div style="width: 75%;">
 
                    <h6 v-if="this.getAppRitiri.filtriLastSrc.scarti">Ricerca Scarti:</h6>
                    <h6 v-else="">Ricerca:</h6>
 
                    <!-- Riepilogo Filtri Inseriti - Inizio -->
                    <span style="float:left;" v-if="this.isFiltroValorizzato('filtro-srcRitiro-dataDa')">
                        <strong>DATA DA:</strong>&nbsp;
                        {{ this.getValoreFiltroData("filtro-srcRitiro-dataDa") }} &nbsp;&nbsp;
                    </span>
                    <span style="float:left;" v-if="this.isFiltroValorizzato('filtro-srcRitiro-dataA')">
                        <strong>DATA A:</strong>&nbsp;
                        {{ this.getValoreFiltroData("filtro-srcRitiro-dataA") }} &nbsp;&nbsp;
                    </span>
                    <span style="float:left;" v-if="this.isFiltroValorizzato('filtro-srcRitiro-codcli')">
                        <strong>CODICE CLIENTE:</strong>&nbsp;
                        {{ this.getValoreFiltro("filtro-srcRitiro-codcli") }} &nbsp;&nbsp;
                    </span>
                    <span style="float:left;" v-if="this.isFiltroValorizzato('filtro-srcRitiro-codrit') && this.isCodRitiro('filtro-srcRitiro-codrit')">
                        <strong>
                            CODICE
                            RITIRO:
                        </strong>&nbsp; {{ this.getValoreFiltro("filtro-srcRitiro-codrit") }} &nbsp;&nbsp;
                    </span>
                    <span style="float:left;" v-if="this.isFiltroValorizzato('filtro-srcRitiro-codrit') && !this.isCodRitiro('filtro-srcRitiro-codrit')">
                        <strong>
                            CODICE
                            PRENOTAZIONE:
                        </strong>&nbsp; {{ this.getValoreFiltro("filtro-srcRitiro-codrit") }} &nbsp;&nbsp;
                    </span>
                    <span style="float:left;" v-if="this.isFiltroValorizzato('filtro-srcRitiro-ragsoc')">
                        <strong>RAGIONE SOCIALE:</strong>&nbsp;
                        {{ this.getValoreFiltro("filtro-srcRitiro-ragsoc") }} &nbsp;&nbsp;
                    </span>
                    <span style="float:left;" v-if="this.isFiltroValorizzato('filtro-srcRitiro-datifisc')">
                        <strong>DATI FISCALI:</strong>&nbsp;
                        {{ this.getValoreFiltro("filtro-srcRitiro-datifisc") }} &nbsp;&nbsp;
                    </span>
                    <span style="float:left;" v-if="this.isFiltroValorizzato('filtro-srcRitiro-localita')">
                        <strong>LOCALITA':</strong>&nbsp;
                        {{ this.getValoreFiltro("filtro-srcRitiro-localita") }} &nbsp;&nbsp;
                    </span>
                    <span style="float:left;" v-if="this.isFiltroValorizzato('filtro-srcRitiro-cap')">
                        <strong>CAP:</strong>&nbsp;
                        {{ this.getValoreFiltro("filtro-srcRitiro-cap") }} &nbsp;&nbsp;
                    </span>
                    <span style="float:left;" v-if="this.isFiltroValorizzato('filtro-srcRitiro-ldv')">
                        <strong>LDV:</strong>&nbsp;
                        {{ this.getValoreFiltro("filtro-srcRitiro-ldv") }} &nbsp;&nbsp;
                    </span>
                    <!-- Riepilogo Filtri Inseriti - Fine -->
 
 
                </div>
 
                <div v-if="this.getAppRitiri.srcResult !== null && this.getAppRitiri.srcResult.length > 0" style="display:flex; flex-direction: row; position:absolute; right:0; margin-top: 6vh;margin-right: 1.5vh;">
 
                    <span class="dropdown-toggle btn btn-xs btn-primary" role="button"
                          @click="esportaPdfExcel('EXCEL')" title="Esporta la tabella in formato excel">Esporta In Excel</span>
                    <span class="dropdown-toggle btn btn-xs btn-primary" style="margin-left: 10px;" role="button"
                          @click="esportaPdfExcel('PDF')" title="Esporta la tabella in formato pdf">Esporta In PDF</span>
                </div>
 
            </div>
            <br /><br />
 
 
 
            <!-- RICERCA -->
            <div>
 
 
                <div class="table-responsive">
                    <vue-good-table :totalRows="this.getAppRitiri.srcResult.length"
                                    :columns="columns"
                                    id="tabSrcResRit"
                                    ref="tabSrcResRit"
                                    :rows="this.getAppRitiri.srcResult"
                                    :fixed-header="false"
                                    :line-numbers="false"
                                    :row-style-class="rowStyleClassFn"
                                    :pagination-options="{
                                        enabled: true,
                                        perPage: 10,
                                        perPageDropdown: [10],
                                        rowsPerPageLabel: 'Righe per Pagina',
                                        nextLabel: 'Successivo',
                                        prevLabel: 'Precedente',
                                        allLabel: 'Tutte',
                                        ofLabel: 'di',
                                        setCurrentPage: this.calcolaPaginaDataodierna()
                                    }"
                                    :sort-options="{
                                      enabled: true,
                                      initialSortBy: { field: 'dataRitiro', type: 'asc' }
                                    }"
                                    :select-options="{
                                        enabled: true,
                                        selectOnCheckboxOnly: true, // only select when checkbox is clicked instead of the row
                                        selectionInfoClass: 'selected',
                                        selectionText: 'rows selected',
                                        clearSelectionText: 'clear',
                                        disableSelectInfo: true, // disable the select info panel on top
                                        selectAllByGroup: true, // when used in combination with a grouped table, add a checkbox in the header row to check/uncheck the entire group
                                      }"
                                    @on-cell-click="onCellClick"
                                    @on-selected-rows-change="selectionChanged">
                        <div slot="emptystate">
                            <strong style="color: red;">Nessun risultato trovato per la ricerca effettuata</strong>
                        </div>
 
                        <template slot="table-column" slot-scope="props">
                            <span>
                                <strong style="color:black; font-size: 16px">{{props.column.label}}</strong>
                            </span>
                        </template>
                        <template slot="table-row" slot-scope="props">
                            <span>
                                <strong>{{props.formattedRow[props.column.field]}}</strong>
                            </span>
                        </template>
 
                        <template slot="table-row" slot-scope="props">
 
                            <span v-if="props.column.field == 'azioni'" style="display:flex">
 
                                &nbsp;
 
                                <span class="dropdown-toggle btn btn-xs " :disabled="checkDisableModifica(props.row)"
                                      role="button" title="Modifica" style="padding: 1px 1px;" alt="Modifica"
                                      src="https://www.poste.it/icone-cta/scrivici.png"
                                      srcset="https://www.poste.it/2X/icone-cta/scrivici.png 2x"
                                      @click.stop="modificaRitiro(props.row)">
 
                                    <img src="https://www.poste.it/icone-cta/scrivici.png"
                                         srcset="https://www.poste.it/2X/icone-cta/scrivici.png 2x" class="spacer-xs-right-10"
                                         alt="Modifica Ritiro"
                                         style="width:4vh; height:4vh;">
 
                                </span>
 
                                &nbsp;&nbsp;&nbsp;
 
                                <span class="dropdown-toggle btn btn-xs "
                                      :disabled="checkDisableRiprogramma(props.row)" role="button" title="Riprogramma"
                                      style="padding:1px;" alt="Riprogramma"
                                      @click.stop="riprogrammaRitiro(props.row)">
 
                                    <img src="https://www.poste.it/img/1476520777393/ico-calcola-tempi-consegna-1x.png"
                                         srcset="https://www.poste.it/img/1476520777393/2X/ico-calcola-tempi-consegna-1x.png 2x"
                                         alt="Riprogramma Ritiro"
                                         style="width:4vh; height:4vh;">
 
                                </span>
 
                            </span>
 
                            <span v-else-if="props.column.field === 'puntoRitiro'">
                                {{getPuntoRitiro(props.row.puntoRitiro)}}
                            </span>
                            <span v-else-if="props.column.field === 'qtaPrenotati'">
                                {{checkAndSetValue(props.row.qtaPrenotati, 'tab', '-')}}
                            </span>
                            <span v-else-if="props.column.field === 'tipologia'">
                                {{checkAndSetValue(props.row.tipologia, 'tab', '-').replaceAll('_', ' ')}}
                            </span>
                            <span v-else-if="props.column.field === 'centroOperativo'">
                                {{checkAndSetValue(props.row.centroOperativo, 'tab', '-')}}
                            </span>
                            <span v-else-if="props.column.field === 'giro'">
                                {{checkAndSetValue(props.row.giro, 'tab', '-')}}
                            </span>
                            <span v-else-if="props.column.field === 'stato'">
                                {{checkAndSetValue(props.row.stato, 'tab', '-')}}
                            </span>
                            <span v-else-if="props.column.field === 'fasciaOrariaRitiro'">
                                {{checkAndSetFasciaOraria(props.row.fasciaOrariaRitiro, '-')}}
                            </span>
                            <span v-else>
                                {{props.formattedRow[props.column.field]}}
                            </span>
 
                        </template>
                    </vue-good-table>
                </div>
 
 
                <!-- PULSANTI -->
                <div style="display:flex; flex-direction: row;">
 
 
                    <!-- ANNULLA/ATTIVA -->
                    <div v-if="this.getAppRitiri.srcResult !== null && this.getAppRitiri.srcResult.length > 0 && !this.getAppRitiri.filtriLastSrc.scarti">
 
                        <!-- PULSANTE DISABILITATO -->
                        <span v-if="this.righeSelezionate.length <= 0" class="dropdown-toggle btn btn-xs btn-primary" style="margin-left: 10px;" role="button" disabled title="Selezionare una riga per annullare o attivare il ritiro">Annulla/Attiva</span>
                        <!-- PULSANTE ABILITATO -->
                        <span v-else class="dropdown-toggle btn btn-xs btn-primary" style="margin-left: 10px;" role="button" @click="annullaAttiva()" title="Annulla o attiva il ritiro selezionato">Annulla/Attiva</span>
 
                    </div>
 
                    <!-- VISUALIZZA STORICO TESTATA RITIRO-->
                    <div>
 
                        <!-- PULSANTE DISABILITATO -->
                        <div v-if="this.getAppRitiri.dettaglioRitiro === null">
                            <span v-if="!this.showStoricoRitiro" class="dropdown-toggle btn btn-xs btn-primary" style="margin-left: 15px;" role="button" disabled title="Seleziona un ritiro per visualizzarne lo storico">Visualizza Storico</span>
                            <span v-else class="dropdown-toggle btn btn-xs btn-primary" style="margin-left: 15px;" role="button" disabled title="Seleziona un ritiro per visualizzarne il dettaglio">Torna al Dettaglio</span>
                        </div>
 
                        <!-- PULSANTE ABILITATO -->
                        <div v-else>
                            <span v-if="!this.showStoricoRitiro" class="dropdown-toggle btn btn-xs btn-primary" style="margin-left: 15px;" role="button" @click="showStoricoTestataRitiro(true)" title="Clicca per visualizzare lo storico del ritiro selezionato">Visualizza Storico</span>
                            <span v-else class="dropdown-toggle btn btn-xs btn-primary" style="margin-left: 15px;" role="button" @click="showStoricoTestataRitiro(false)" title="Clicca per visualizzare il dettaglio del ritiro selezionato">Torna al Dettaglio</span>
                        </div>
 
                    </div>
 
                </div>
 
 
            </div>
 
 
 
 
            <!-- STORICO O DETTAGLIO RITIRO -->
            <div v-if="this.showStoricoRitiro">
                <!-- STORICO DEL RITIRO -->
                <StoricoTestataRitiro />
            </div>
            <div v-else>
                <!-- DETTAGLIO -->
                <DettaglioRitiro />
            </div>
 
 
 
        </div>
        <div v-else style="height:38vh">
            <!-- Div per assottigliare il footer -->
        </div>
 
 
 
        <!-- LABEL NASCOSTA PER APRIRE LA MODALE DI MODIFICA RITIRO QUANDO VENGO DA ERMES - EFFETTUANDO LA RICERCA PER ID-RITIRO AVRO SEMPRE UN SOLO RISULTATO IN TABELLA - CHIAMATA SOLO DALLA ACTION ricercaRitiroErmes -->
        <span id="modificaRitiroErmes" @click="modRiprogErmes('MODIFICA')" hidden>Modifica ritiro ermes</span>
 
        <!-- LABEL NASCOSTA PER APRIRE LA MODALE DI RIPROGRAMMA RITIRO QUANDO VENGO DA ERMES - EFFETTUANDO LA RICERCA PER ID-RITIRO AVRO SEMPRE UN SOLO RISULTATO IN TABELLA - CHIAMATA SOLO DALLA ACTION ricercaRitiroErmes -->
        <span id="riprogrammaRitiroErmes" @click="modRiprogErmes('RIPROGRAMMA')" hidden>Riprogramma ritiro ermes</span>
 
 
    </div>
 
</template>
 
 
<script>
 
    import { mapActions, mapGetters } from "vuex";
 
    //- Librerie per PDF/Excel
    import * as XLSX from 'sheetjs-style';
    import jsPDF from "jspdf";
    import autoTable from 'jspdf-autotable';
 
 
    export default {
 
 
        data() {
            return {
                showStoricoRitiro: false,
                beforeRowSelect: null,
                perPage: 5,
                currentPage: 1,
                righeSelezionate: [],
                fields: ['idRitiro', 'qtaPrenotati', 'puntoRitiro', 'tipologia', 'dataRitiro', 'fasciaOrariaRitiro', 'centroOperativo', 'giro', 'stato'],
 
 
                columns: [
                    {
                        label: "ID Ritiro",
                        field: "idRitiro",
                    },
                    {
                        label: "Qta Prenotati",
                        field: "qtaPrenotati",
                    },
                    {
                        label: "Punto Ritiro",
                        field: "puntoRitiro",
                    },
                    {
                        label: "Tipologia",
                        field: "tipologia",
                    },
                    {
                        label: "Data Ritiro",
                        field: "dataRitiro",
                        type: 'date',
                        dateInputFormat: 'yyyy-MM-dd',
                        dateOutputFormat: 'dd/MM/yyyy',
                    },
                    {
                        label: "Fascia Oraria Ritiro",
                        field: "fasciaOrariaRitiro",
                    },
                    {
                        label: "Centro Operativo",
                        field: "centroOperativo",
                    },
                    {
                        label: "Giro",
                        field: "giro",
                    },
                    {
                        label: "Stato",
                        field: "stato",
                    },
                    {
                        label: "Azioni",
                        field: "azioni",
                    }
                ],
 
 
            }
        },
 
 
 
        computed: {
            ...mapGetters("views", ["userInfo", "getAppRitiri"]),
        },
 
        methods: {
            ...mapActions("views", ["showRitiriDett", "showRitiriStorico", "setModaleAppOneLogistic", "annullaAttivaRitiro", "modificaTestataRitiro", "riprogrammaTestataRitiro"]),
 
 
 
            //- Calcola la pagina dove si trova il primo ritiro con la data odierna per settare il focus sul ritiro odierno
            calcolaPaginaDataodierna() {
 
                try {
 
                    //- Copio il risultato della ricerca
                    let orderTableRow = new Array();
                    Object.assign(orderTableRow, this.getAppRitiri.srcResult);
 
                    //- Lo ordino per data decrescente come in tabella
                    orderTableRow.sort((a, b) => (new Date(a.dataRitiro) < new Date(b.dataRitiro) ? -1 : 1));
 
                    //- Recupero la  Data Odierna
                    const date = new Date();
                    let todayStr = date.toISOString().slice(0, 10);
 
 
                    //- Calcolo la pagina
                    let index = 1;
                   
                    for (let r of orderTableRow) {
 
                        if (r.dataRitiro >= todayStr) {  //- Sono ordinati in maniera crescente quindi prendo la data ritiro uguale o superiore alla data odierna per lasciare indietro le prenotazioni passate
 
                            //- index contiene la posizione della data odierna tra i risultati
                            //- Sapendo che la paginazione e' di 10 record calcolo la pagina da settare
 
                            let page = index / 10;
 
                            if (Number.isInteger(page)) //- Perche' la riga 10 e' a pagina 1
                                return page;
                            else
                                return (Math.trunc(page) + 1);
 
                        }
 
                        index++;
                    }
 
                    //- Ritorno la prima pagina in caso non trovo la data odierna tra i risultati
                    return 1;
 
                }
                catch (e) {
               
                    //- Ritorno la prima pagina in caso di errore
                    return 1;
                }
               
            },
 
 
           
 
 
            onRowClick(params) {
 
                //- Gestisco il click della riga
                params.row.cliccato = !params.row.cliccato; //- Aggiunto in mutation al mapping dei dati, settato false
 
 
                if (this.beforeRowSelect !== params.row) {
 
                    if (this.beforeRowSelect !== null)
                        this.beforeRowSelect.cliccato = false
 
                    this.beforeRowSelect = params.row;
                }
 
 
                if (params.row.cliccato) {
 
                    for (let d of params.row.prenotazione) {
 
                        if (d.dataPrenotazione !== null && d.dataPrenotazione !== undefined
                            && d.dataPrenotazione !== 'undefined' && d.dataPrenotazione !== "")
                            d.dataPrenotazione = d.dataPrenotazione.split(".")[0].replace("T", " ");
 
 
                        //- Copio dal risultato ricerca al dettaglio i campi 'dataRitiro' e 'fasciaOraria'
                        //- per un'eventuale operazione di modifica/riprogramma
                        d.dataRitiro = params.row.dataRitiro;
                        d.fasciaOraria = params.row.fasciaOrariaRitiro;
                        d.puntoRitiro = params.row.puntoRitiro;
                        //-  d.orarioUps = params.row..orarioUps;
                    }
 
                    this.showRitiriDett(params.row.prenotazione);
                }
                else {
                    this.showStoricoRitiro = false;
                    this.showRitiriDett(null);
                    this.showRitiriStorico(null);
                }
 
            },
 
 
 
 
            onCellClick(params) {
                this.onRowClick(params)
            },
 
 
 
 
            //- Visualizza/Nasconde il dettaglio ritiro e visualizza lo storico
            showStoricoTestataRitiro(val) {
 
                this.showStoricoRitiro = val;
            },
 
 
 
            //- Tiene in memoria le righe selezioante
            selectionChanged(params) {
           
                this.righeSelezionate = params.selectedRows;
            },
 
 
 
 
 
            //- Per eventuale controllo sull'attivazione della funzionalita
            checkDisableModifica(riga) {
 
                if (riga.modificabile) //- Se modificabile allora setto disabled=false;
                    return false;
 
                return true;
 
            },
 
 
 
            //- Per eventuale controllo sull'attivazione della funzionalita
            checkDisableRiprogramma(riga) {
 
                if (riga.riprogrammabile) //- Se riprogrammabile allora setto disabled=false;
                    return false;
 
                return true;
            },
 
 
 
 
 
            //- Apre in automatico la modifica o riprogrammazione del ritiro da Ermes
            modRiprogErmes(tipologia) {
 
                //- Chiudo il menù di ricerca, eseguo il comando una seconda volta perchè non funziona al primissimo click del pulsante
                document.getElementById("tabDiRicercaRitiri").classList.remove("active"); //- Web.vue riga 29
                setTimeout(() => { document.getElementById("tabDiRicercaRitiri").classList.remove("active"); }, 70);
 
                if (tipologia === 'MODIFICA')
                    this.modificaTestataRitiro(this.getAppRitiri.srcResult[0]);
                else
                    this.riprogrammaTestataRitiro(this.getAppRitiri.srcResult[0]);
            },
 
 
 
 
            //- Funzione di modifica Ritiro
            modificaRitiro(riga) {
 
                if (this.checkDisableModifica(riga)) //- Se il tasto e' disabilitato allora non faccio nulla
                    return;
 
                this.modificaTestataRitiro(riga);
 
            },
 
 
 
            //- Funzione di riprogramma Ritiro
            riprogrammaRitiro(riga) {
 
                if (this.checkDisableRiprogramma(riga)) //- Se il tasto e' disabilitato allora non faccio nulla
                    return;
 
                this.riprogrammaTestataRitiro(riga);
            },
 
 
 
 
            //- Annulla/Riattivo il ritiro
            annullaAttiva() {
 
                //- Controllo se la riga selezionata può essere annullata/riattivata
                var msg = this.checkAnnullaAttivaPossible();
 
                //- Se il controllo non risponde OK allora conterrà il messaggio di errore da mostrare a video
                if (msg !== "OK") {
 
                    //- Stampa errore
                    let err = new Object();
                    err.show = true;
                    err.titolo = 'Operazione Annullata!';
                    err.messaggio = msg;
 
                    //- Stampo il messaggio di Errore a video
                    this.setModaleAppOneLogistic(err);
 
                    return;
                }
 
 
                //- Posso Annullare/Attivare il ritiro quindi costruisco la request ed invoco la action
                this.annullaAttivaRitiro(this.getAnnAttReq());
               
            },
 
 
 
 
 
            //- Metodo che controlla se posso Annullare o Attivare il ritiro
            checkAnnullaAttivaPossible() {
 
                //- Regole:
                //- 1) Non è possibile attivare/annullare elementi misti (Tutti annullabili/attivabili, non misti)
                //-        "Selezionare solo ritiri con lo stesso stato"
                //- 2) DATA RITIRO (dataRitiro nel json) >= DATA CORRENTE gestita lato backend
                //- --------------------------------------------------------------------------
                //- Nota_1) ANNULLA : posso Solo se lo stato del ritiro è "DA EFFETTUARE"  --> setto attiva:false nella request                
                //- Nota_2) ATTIVA :  posso Solo se lo stato del ritiro è "RITIRO ANNULLATO"  --> setto attiva:true nella request
 
                //- Restituisce OK se posso eseguire l'operazione altrimenti un messaggio di errore da stampare a video
                var esito = "OK";
 
                //- Controlli secondo le regole - 1
                var lastStatus = this.righeSelezionate[0].stato;
                var dataOdierna = new Date();
 
                for (var r of this.righeSelezionate) {
 
                    //- Regola 1
                    if (lastStatus !== r.stato)
                        return "Tutti i ritiri selezionati devono trovarsi nello stesso stato";
 
                    lastStatus = r.stato;
 
                }
 
                //- Regole soddisfatte
                return esito;
 
            },
 
 
 
 
 
 
            //- Al popolamento della tabella se ho un solo record allora lo seleziono
            checkRecordUnico() {
 
 
                if (this.getAppRitiri.srcResult === null || this.getAppRitiri.srcResult === undefined || this.getAppRitiri.srcResult === 'undefined')
                    return;
 
                //- console.log(this.$refs.myTable.filteredRows[0].children)
                //- document.getElementById("vgt-table").getElementsByTagName("tbody")[0].getElementsByTagName("tr")[0].getElementsByTagName("th")[0].getElementsByTagName("input")[0].checked = true;
 
                //- se ho un solo risultato risulta già 'cliccato' (flag cliccato) per visualizzare la sezione del dettaglio
                //- Ma occorre anche selezionare la riga della tabella
                if (this.getAppRitiri.srcResult.length === 1) {                    
 
                    //- Seleziono la riga della tabella
                    this.$refs.tabSrcResRit.filteredRows[0].children[0].vgtSelected = true;
 
                    this.righeSelezionate = new Array();
                    //this.righeSelezionate.push(this.$refs.myTable.filteredRows[0].children);
                    this.righeSelezionate.push(this.getAppRitiri.srcResult[0]);
                   
                }
 
            },
 
 
 
 
            //- Costruisce la request del servizio
            getAnnAttReq() {
 
                //- Esempio : [{ "idRitiro": 5, "attiva": true }]
                let req = new Array();
 
                for (var r of this.righeSelezionate) {
 
                    let ritiro = new Object();
                    ritiro.idRitiro = Number(r.idRitiro);
                    ritiro.attiva = true;
                    if (r.stato === "DA EFFETTUARE")
                        ritiro.attiva = false;
 
                    req.push(ritiro);
                }                
             
                return req;
            },
 
 
 
 
 
            //- Formatta il punto ritiro (indirizzo) per visualizzarlo a video
            getPuntoRitiro(puntoRitiro) {
 
                let ritiro = puntoRitiro.nome + " " + puntoRitiro.cognome + ", " + puntoRitiro.indirizzo + " " + puntoRitiro.civico;
                ritiro = ritiro + this.checkAndSetValue(puntoRitiro.km, "km", "");
                ritiro = ritiro + this.checkAndSetValue(puntoRitiro.localita, "", "");
                ritiro = ritiro + this.checkAndSetValue(puntoRitiro.provincia, "provincia", "");
                ritiro = ritiro + this.checkAndSetValue(puntoRitiro.cap, "", "");
                ritiro = ritiro + this.checkAndSetValue(puntoRitiro.palazzina, "palazzina", "");
                ritiro = ritiro + this.checkAndSetValue(puntoRitiro.scala, "scala", "");
                ritiro = ritiro + this.checkAndSetValue(puntoRitiro.piano, "piano", "");
                ritiro = ritiro + this.checkAndSetValue(puntoRitiro.interno, "interno", "");
 
                return ritiro;
            },
 
 
 
            //- Se 'val' è valorizzato allora return di descrizione+val, altrimenti return def
            checkAndSetValue(val, descr, def) {
 
                if (val === null || val === undefined || val === 'undefined' || val.trim() === '')
                    return def;
 
                if (descr === 'provincia')
                    return " (" + val + ")";
                else if (descr === 'tab')
                    return val;
                else if (descr === '')
                    return ", " + val;
                else
                    return ", " + descr + " " + val;
            },
 
 
 
 
 
            //- Traduce la fascia oraria o se non valorizzata ne restituisce un default
            checkAndSetFasciaOraria(val, def) {
 
                if (val === undefined || val === 'undefined' || val.trim() === '')
                    return def;
                else if (val === 'AM')
                    return "Mattina";
                else if (val === 'PM')
                    return "Pomeriggio";
                else
                    return "Tutta la giornata";
            },
 
 
 
 
 
            getValoreFiltro(id) {
 
                return document.getElementById(id).value;
            },
 
 
            isCodRitiro(id) {
 
                //- Restituisce TRUE se è un codice Ritiro, FALSE se è un codice Prenotazione
                //- Il codice Prenotazione inizia per Lettera, il codice Ritiro invece inizia per numero
                let codice = document.getElementById(id).value;
 
                if (codice !== 'undefined' && codice !== null && codice !== '') {
 
                    let iniziale = Array.from(codice)[0];
 
                    return !isNaN(Number(iniziale));
                }
 
                return false; //- In questo caso il filtro non è valorizzato quindi non verrà visualizzato
            },
 
 
            getValoreFiltroData(id) {
 
                //- La data è nel formato yyyy-MM-dd quindi se valorizzata la riformatto in dd/MM/yyyy
                let data = document.getElementById(id).value;
 
                if (data !== 'undefined' && data !== null && data !== '' && data.includes("-")) {
 
                    data = data.split("-")[2] + "/" + data.split("-")[1] + "/" + data.split("-")[0];
                }
 
                return data;
            },
 
 
            isFiltroValorizzato(id) {
 
                if (document.getElementById(id) !== undefined && document.getElementById(id) !== 'undefined' && document.getElementById(id) !== null) {
                    if (document.getElementById(id).value !== 'undefined' && document.getElementById(id).value !== null && document.getElementById(id).value !== '')
                        return true;
                }
               
 
                return false;
            },
 
 
 
 
 
 
            //- Setta la data nel formato DD/MM/YYYY
            formattaDataPerExport(dt) {
 
                if (dt === null || dt === undefined || dt === 'undefined' || dt === '' || dt.length <= 1)
                    return dt;
 
                if (dt.includes("-")) {
 
                    var splitDt = dt.split("-");
                    return "" + splitDt[2] + "/" + splitDt[1] + "/" + splitDt[0];
                }
 
                return dt;
            },
 
 
 
 
            //- Ritorna i filtri inseriti per la ricerca ritiro per stamparli a video
            getFiltriRicercaPerExport() {
 
                var filtri = "";
 
                filtri = this.concatenaFiltriSrc(filtri, "DATA DA: ", "filtro-srcRitiro-dataDa");
                filtri = this.concatenaFiltriSrc(filtri, "DATA A: ", "filtro-srcRitiro-dataA");
                filtri = this.concatenaFiltriSrc(filtri, "CODICE CLIENTE: ", "filtro-srcRitiro-codcli");
 
                if (this.isFiltroValorizzato('filtro-srcRitiro-codrit') && !this.isCodRitiro('filtro-srcRitiro-codrit'))
                    filtri = this.concatenaFiltriSrc(filtri, "CODICE PRENOTAZIONE: ", "filtro-srcRitiro-codrit");
                else
                    filtri = this.concatenaFiltriSrc(filtri, "CODICE RITIRO: ", "filtro-srcRitiro-codrit");
 
                filtri = this.concatenaFiltriSrc(filtri, "RAGIONE SOCIALE: ", "filtro-srcRitiro-ragsoc");
                filtri = this.concatenaFiltriSrc(filtri, "DATI FISCALI: ", "filtro-srcRitiro-datifisc");
                filtri = this.concatenaFiltriSrc(filtri, "LDV: ", "filtro-srcRitiro-ldv");
                filtri = this.concatenaFiltriSrc(filtri, "LOCALITA': ", "filtro-srcRitiro-localita");
                filtri = this.concatenaFiltriSrc(filtri, "CAP ", "filtro-srcRitiro-cap");
           
                return filtri;
            },
 
 
 
 
            //- Concatena i filtri se valorizzati
            concatenaFiltriSrc(filtri, desc, id) {
 
                if (this.isFiltroValorizzato(id))
                    filtri = filtri + " " + desc + this.getValoreFiltroData(id) + ";   ";
 
                return filtri;
            },
 
 
 
 
            //- Export PDF/Excel
            esportaPdfExcel(estensione) {
 
                const pdf = new jsPDF("l", "pt", "a4")
                let excel = [];
                let name = "Ricerca_Ritiro";  //- Nome file
               
 
                //- Intestazione PDF
                this.creaIntestazionePdf(pdf, 16, 'Risultato Ricerca Ritiro');
                this.getFiltriRicercaPerExport()
                //- Aggiunta riepilogo filtri ricerca al pdf
                this.creaIntestazionePdf(pdf, 11, 'Filtri di ricerca inseriti:');
                this.creaIntestazionePdf(pdf, 10, this.getFiltriRicercaPerExport());
 
                //- Intestazione Excel
                excel.push(['']);
                excel.push(['']);
                excel.push(['']);
                excel.push(['Risultato Ricerca Ritiro']);
                excel.push(['']);
                excel.push(['Filtri di ricerca inseriti:']);
                excel.push([this.getFiltriRicercaPerExport()]);
                excel.push(['']);
                excel.push(['ID Ritiro', 'Qta Prenotati', 'Punto Ritiro', 'Tipologia', 'Data Ritiro', 'Fascia Oraria Ritiro', 'Centro Operativo', 'Giro', 'Stato']);
 
 
                //- Tabella Storico Lavorazione Ritiro : Aggiunta record alle tabelle PDF ed Excel
 
                let doc = [];
 
                for (const r of this.getAppRitiri.srcResult) {
 
                    let row = [];
 
                    row.push(r.idRitiro);
                    row.push(this.checkAndSetValue(r.qtaPrenotati, 'tab', '-'));
                    row.push(this.getPuntoRitiro(r.puntoRitiro));
                    row.push(this.checkAndSetValue(r.tipologia, 'tab', '-'));
                    row.push(this.formattaDataPerExport(r.dataRitiro));
                    row.push(this.checkAndSetFasciaOraria(r.fasciaOrariaRitiro, '-'));
                    row.push(this.checkAndSetValue(r.centroOperativo, 'tab', '-'));
                    row.push(this.checkAndSetValue(r.giro, 'tab', '-'));
                    row.push(this.checkAndSetValue(r.stato, 'tab', '-'));
 
                    doc.push(row);    //- Aggiunta al file PDF
                    excel.push(row);  //- Aggiunta al file Excel
                }
 
                //- Creazione intestazione colonne PDF
                let intestazione = [['ID Ritiro', 'Qta Prenotati', 'Punto Ritiro', 'Tipologia', 'Data Ritiro', 'Fascia Oraria Ritiro', 'Centro Operativo', 'Giro', 'Stato']];
                this.scriviTabPdf(pdf, intestazione, doc); //- Scrittura In PDF
 
 
 
                //- Generazione del File
                if (estensione === 'PDF') {
 
                    name = name + ".pdf";
                    pdf.save(name)
                }
                else {  //- estensione === 'EXCEL'
 
                    let nameFile = name + ".xlsx"
 
                    var wb = XLSX.utils.book_new();
                    var tab = XLSX.utils.aoa_to_sheet(excel);
                    this.settaTitoliInGrassetto(excel, tab);
                    XLSX.utils.book_append_sheet(wb, tab, name);
                    XLSX.writeFile(wb, nameFile);
                }
 
            },
 
 
 
 
            //- ---------------------------------
            creaIntestazionePdf(pdf, size, titolo) {
 
                autoTable(pdf, {
                    theme: 'plain',
                    headStyles: { fontSize: size },
                    head: [[titolo]],
                    body: []
                })
 
            },
 
            //- ---------------------------------
            scriviTabPdf(pdf, intestazione, righe) {
 
                autoTable(pdf, {
                    theme: 'grid',
                    headStyles: { fillColor: '#0060AE' },
                    head: intestazione,
                    body: righe
                })
 
            },
 
 
 
            //- ---------------------------------
            settaTitoliInGrassetto(excel, tab) {
 
                //- Le celle dei titoli di solito sono precedute da spazi, quindi scorro il file e se ho la riga precedente BLANK allora va in grassetto
                let lecters = ['', 'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z'];
                let cellPrec = "";
                let index = "";
 
                for (let i = 1; i <= excel.length; i++) {
 
                    if (cellPrec === "") {
 
                        for (let k = 1; k <= lecters.length; k++) {
 
                            try {
 
                                index = lecters[k] + i;
 
                                tab[index].s = {
                                    font: {
                                        bold: true,
                                        color: "#F2F2F2"
                                    },
                                }
 
                            }
                            catch (error) {
 
                            }
                        }
                    }
 
                    //- Tengo traccia della cella precedente
                    try {
                        index = lecters[1] + i;
                        cellPrec = tab[index].v;
                    }
                    catch (error) {
 
                    }
 
                }
 
            },
 
 
 
 
 
            rowStyleClassFn(row) {
 
                //- GRIGIO POSTE SE SELEZIONATA
                if (row.cliccato)
                    return 'VGT-row-grey';
 
                //- STANDARD
                return 'VGT-row-standard';
 
            },
 
 
 
 
 
 
 
 
        },
 
 
 
        mounted() {
 
          //- Al popolamento della tabella se ho un solo record allora lo seleziono
          this.checkRecordUnico();
 
        },
 
 
    }
</script>
 
<style>
    .VGT-row-grey {
        background-color: #f1f1f1;
        cursor: pointer;
    }
 
        .VGT-row-grey:hover {
            cursor: pointer;
        }
 
 
 
    .VGT-row-standard {
        cursor: pointer;
    }
 
        .VGT-row-standard:hover {
            cursor: pointer;
        }
 
 
 
    .VGT-row-red {
        background-color: #ff6363;
    }
 
        .VGT-row-red:hover {
            background-color: #fe7575;
            cursor: pointer;
        }
</style>
 
<style scoped>
    /deep/ .green-bg {
        background-color: #F6F6F6;
        border: solid 1px #DDD;
    }
</style>
 
<style scoped>
    /deep/ .body-bg {
        border: solid 1px #DDD;
    }
</style>
 
 