<template>
    <div class="page--predictions">

        <div class="day" v-for="(gameDate, games) in gamesByDay">

            <card-title class="gameDate">{{* gameDate | date}}</card-title>
            <card-list wide class="games">

                <card wide class="game"
                        :class="{ 'game--submissionDisabled': isSubmissionClosed(game), 'game--unsaved' : game.unsaved }"
                        v-for="game in games" track-by="gameId">

                    <div class="game-header">
                        <div class="game-name">
                            {{* game.gameName}} -
                            <span v-if="game.phase ==='Groupes'">Groupe {{* game.group}}</span>
                            <span v-else>{{* game.phase}}</span>
                        </div>
                        <div class="game-location">{{* game.stadium}} ({{* game.city}})</div>
                    </div>

                    <div class="game-teams">
                        <div class="game-teams-section">
                            <img v-if="game.codeTeamA" class="flag" :src="'/static/flags/' + game.codeTeamA + '.svg'"/>
                            <img v-if="!game.codeTeamA" class="flag unknownTeam" src="../assets/unknown-team.svg">
                            <div class="game-countryName">{{* game.nameTeamA}}</div>
                        </div>
                        <div class="game-teams-section">
                            <div v-if="hasScore(game) && isResultPublishable(game)"
                                 class="game-score">{{* game.goalsTeamA}} - {{* game.goalsTeamB}}
                            </div>
                            <div v-if="hasScoreWithPenalties(game) && isResultPublishable(game)"
                                 class="game-penalties">Tab. {{* game.penaltiesTeamA}} - {{* game.penaltiesTeamB}}
                            </div>
                            <div v-if="!hasScore(game) || !isResultPublishable(game)" class="game-time">{{* game.startsAt | time}}</div>
                        </div>
                        <div class="game-teams-section">
                            <img v-if="game.codeTeamB" class="flag" :src="'/static/flags/' + game.codeTeamB + '.svg'"/>
                            <img v-if="!game.codeTeamB" class="flag unknownTeam" src="../assets/unknown-team.svg">
                            <div class="game-countryName">{{* game.nameTeamB}}</div>
                        </div>
                    </div>

                    <div class="game-inputs">
                        <div class="game-scoreInput">
                            <input v-model="game.predictionScoreTeamA" @change="setPredictionUnsaved(game)"
                                    class="game-scoreInputField" type="number" name="goalsTeamA"
                                    onfocus="this.select()"
                                    :disabled="isSubmissionClosed(game)"/>
                        </div>
                        <div class="game-scoreInput"><!-- Dummy element to align flex items --></div>
                        <div class="game-scoreInput">
                            <input v-model="game.predictionScoreTeamB" @change="setPredictionUnsaved(game)"
                                    class="game-scoreInputField" type="number" name="goalsTeamB"
                                    onfocus="this.select()"
                                    :disabled="isSubmissionClosed(game)"/>
                        </div>
                    </div>

                    <div class="game-risk">
                        <span v-if="game.riskHappened == null || !isResultPublishable(game)"
                                class="game-risk-titlePrefix">Risquette :</span>
                        <span v-if="game.riskHappened === true && isResultPublishable(game)"
                                class="game-risk-titlePrefix"
                                :class="{ rightAnswer: wasRightAboutRisk(game) === true, wrongAnswer: wasRightAboutRisk(game) === false }">Risquette vraie :</span>
                        <span v-if="game.riskHappened === false && isResultPublishable(game)"
                                class="game-risk-titlePrefix"
                                :class="{ rightAnswer: wasRightAboutRisk(game) === true, wrongAnswer: wasRightAboutRisk(game) === false }">Risquette fausse :</span>
                        <span class="game-risk-title">{{* game.riskTitle}}</span>

                        <div class="game-risk-input">
                            <div class="game-risk-answer game-risk-trueOrFalse">
                                <div class="game-risk-answerHeader">Réponse</div>

                                <div class="game-risk-answerChoiceGroup">
                                    <div class="game-risk-answerChoice">
                                        <input v-model="game.predictionRiskAnswer" type="radio" :value="true"
                                                @change="setPredictionUnsaved(game)" name="riskAnswer{{* game.gameId}}"
                                                id="yes{{* game.gameId}}"
                                                :disabled="isSubmissionClosed(game)"/>
                                        <label for="yes{{* game.gameId}}">VRAI</label>
                                    </div>

                                    <div class="game-risk-answerChoice">
                                        <input v-model="game.predictionRiskAnswer" type="radio" :value="false"
                                                @change="setPredictionUnsaved(game)" name="riskAnswer{{* game.gameId}}"
                                                id="no{{* game.gameId}}"
                                                :disabled="isSubmissionClosed(game)"/>
                                        <label for="no{{* game.gameId}}">FAUX</label>
                                    </div>

                                    <div class="game-risk-answerChoice noAnswer">
                                        <input v-model="game.predictionRiskAnswer" type="radio" :value="null"
                                                @change="setPredictionUnsaved(game)" name="riskAnswer{{* game.gameId}}"
                                                id="dunno{{* game.gameId}}"
                                                :disabled="isSubmissionClosed(game)"/>
                                        <label class="game-risk-answerChoice--multiline"
                                                for="dunno{{* game.gameId}}">Je ne sais pas</label>
                                    </div>
                                </div>
                            </div>
                            <div class="game-risk-answer game-risk-riskedPoints">
                                <div class="game-risk-answerHeader">Risquer</div>
                                <div class="game-risk-answerNoRisk" v-show="game.predictionRiskAnswer == null">
                                    Aucun point risqué
                                </div>
                                <div v-show="game.predictionRiskAnswer != null"
                                        class="game-risk-answerChoiceGroup">
                                    <div class="game-risk-answerChoice">
                                        <input v-model="game.predictionRiskAmount" type="radio" :value="1"
                                                @change="setPredictionUnsaved(game)" name="riskAmount{{* game.gameId}}"
                                                id="riskAmount1{{* game.gameId}}"
                                                :disabled="isSubmissionClosed(game)"/>
                                        <label for="riskAmount1{{* game.gameId}}">1 pt</label>
                                    </div>

                                    <div class="game-risk-answerChoice">
                                        <input v-model="game.predictionRiskAmount" type="radio" :value="2"
                                                @change="setPredictionUnsaved(game)" name="riskAmount{{* game.gameId}}"
                                                id="riskAmount2{{* game.gameId}}"
                                                :disabled="isSubmissionClosed(game)"/>
                                        <label for="riskAmount2{{* game.gameId}}">2 pts</label>
                                    </div>

                                    <div class="game-risk-answerChoice">
                                        <input v-model="game.predictionRiskAmount" type="radio" :value="3"
                                                @change="setPredictionUnsaved(game)" name="riskAmount{{* game.gameId}}"
                                                id="riskAmount3{{* game.gameId}}"
                                                :disabled="isSubmissionClosed(game)"/>
                                        <label for="riskAmount3{{* game.gameId}}">3 pts</label>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>

                    <div class="game-submitZone">
                        <btn :inactive="game.unsaved !== true || !predictionIsValid(game)"
                                class="game-submitZone-button"
                                :class="{ disabled: !predictionIsValid(game) }"
                                @click="savePrediction(game)"
                                :disabled="isSubmissionClosed(game)">Enregistrer
                        </btn>
                        <div class="game-submitZone-savedCheck" v-show="showPredictionSavedTick(game)"></div>
                        <div class="game-pointsExplanation"
                                v-if="isResultPublishable(game) && game.points != null && game.points < 8">
                            {{* game.points}} pts : {{* game.classicPoints}} pts {{* game.riskPoints >= 0 ? '+' : '-'}}
                            {{* (game.riskPoints || 0) | abs}} pts (risquette)
                        </div>
                        <div class="game-pointsExplanation"
                                v-if="isResultPublishable(game) && game.points == 8">
                            Huit parfait !
                        </div>
                    </div>

                </card>
            </card-list>
        </div>

    </div>
</template>

<script type="text/babel">
    import Vue from 'vue'
    import store from '../state/configureStore'
    import { fetchPredictions, savePrediction } from '../state/actions/predictions'
    import _ from 'lodash'
    import moment from 'moment'

    moment.locale('fr')

    export default {
        data() {
            return {
                predictions: this.$select('predictions'),
                gamesByDay: null,
            }
        },
        route: {
            data() {
                switch (this.$route.params.period) {
                    case 'matchs-precedents':
                        return store.dispatch(fetchPredictions('previous-days'))
                    case 'prochains-matchs':
                        return store.dispatch(fetchPredictions('next-days'))
                    default:
                        return store.dispatch(fetchPredictions())
                }
            },
        },
        watch: {
            predictions(predictions) {

                if (predictions == null) {
                    this.gamesByDay = null
                    return
                }

                this.gamesByDay = _.cloneDeep(predictions)
            },
        },
        methods: {
            hasScore: function (game) {
                return game.goalsTeamA != null &&
                    game.goalsTeamB != null
            },

            hasScoreWithPenalties: function (game) {
                return this.hasScore(game) &&
                    game.penaltiesTeamA != null &&
                    game.penaltiesTeamB != null
            },
            setPredictionUnsaved: function (game) {
                Vue.set(game, 'unsaved', true)
            },
            setPredictionSaved: function (game) {
                Vue.set(game, 'unsaved', false)
            },
            showPredictionSavedTick: function (game) {
                // Do not show the tick when the results are available
                // Show it when the game has just been saved (game.unsaved === false)
                // Also show it when we've just laoded the page and the prediction is valid (game.unsaved == null but prediction valid)
                return !this.isResultPublishable(game) && (game.unsaved === false || (game.unsaved == null && this.predictionIsValid(game)))
            },
            isSubmissionClosed: function (game) {
                // Submissions close as soon as the game begins
                return moment(game.startsAt).isBefore(Date.now())
            },
            isResultPublishable: function (game) {
                // A result is publishable at 8:08AM the next day of a game
                return moment(game.startsAt).endOf('day').add(8, 'hours').add(8, 'minutes').isBefore(Date.now())
            },
            wasRightAboutRisk: function (game) {
                if (game.predictionRiskAnswer == null) {
                    return null
                }
                return game.predictionRiskAnswer === game.riskHappened
            },
            predictionIsValid: function (game) {
                // Wrong value types in fields
                if (!_.isInteger(parseInt(game.predictionRiskAmount))
                  || game.predictionRiskAmount <= 0
                  || !_.isInteger(parseInt(game.predictionScoreTeamA))
                  || game.predictionScoreTeamA < 0
                  || !_.isInteger(parseInt(game.predictionScoreTeamB))
                  || game.predictionScoreTeamB < 0) {
                    return false
                }

                // No risk amount selected even though an answer to the risk is provided
                if (game.predictionRiskAnswer != null &&
                        game.predictionRiskAmount <= 0) {
                    return false
                }

                return true
            },
            savePrediction: function (game) {
                if (game.unsaved !== true || !this.predictionIsValid(game)) {
                    return
                }

                store.dispatch(savePrediction(game))
                        .then(() => {
                            this.setPredictionSaved(game)
                        })
                        .catch(() => {
                            this.setPredictionUnsaved(game)
                        })
            },
        },
        filters: {
            date: function (gameTime) {
                return moment(gameTime).format('dddd Do MMMM')
            },
            time: function (gameTime) {
                return moment(gameTime).format('HH[h]mm')
            },
            abs: function (number) {
                return Math.abs(number)
            },
        },
    }
</script>

<style scoped>

    .gameDate {
        text-transform: capitalize;
    }

    .card.game {
        padding: 0;
        padding-bottom: 60px;
    }

    .game {
        background-color: #fff;
        border-bottom: 2px solid #ddd;
        box-sizing: border-box;
        margin-bottom: 15px;
        overflow: hidden;
        position: relative;
        transition: background-color 0.2s,
    }

    @media (min-width: 500px) {
        .game {
            border: 1px solid #ddd;
            border-bottom-width: 2px;
            border-radius: 4px;
            margin: 0 8px 15px 8px;
        }
    }

    .game.game--unsaved {
        background-color: #FFFFCC;
        transition: background-color 0.2s,
    }

    .game-header {
        background: #eee;
        border-bottom: 1px solid #ddd;
        padding: 5px 15px;
    }

    .game-name {
        font-size: 15px;
        font-weight: bold;
    }

    .game-location {
        font-size: 15px;
        font-style: italic;
    }

    .game-teams {
        background: white;
        border-bottom: 1px dashed #ddd;
        display: flex;
        flex-direction: row;
        padding: 25px 15px 15px 15px;
    }

    .game-teams-section {
        flex: 1 1 0;
        text-align: center;
    }

    .flag {
        border: 1px solid #DDD;
        border-bottom-width: 2px;
        border-radius: 4px;
        height: 80px;
        width: 80px;
    }

    .game-countryName {
        color: #333;
        font-weight: bold;
    }

    .game-time {
        color: #555;
        font-size: 18px;
        font-weight: bold;
        margin-top: 25px;
    }

    .game-score {
        color: #49996f;
        font-size: 20px;
        font-weight: bold;
        margin-top: 25px;
    }

    .game-penalties {
        color: #b50101;
        font-size: 15px;
        font-weight: bold;
    }

    .game-inputs {
        display: flex;
        flex-direction: row;
        padding: 20px 0 5px;
    }

    .game-scoreInput {
        flex: 1 1 0;
    }

    .game-scoreInputField {
        border: 1px solid #ddd;
        border-bottom-width: 2px;
        border-radius: 4px;
        display: block;
        font-size: 20px;
        height: 30px;
        margin: auto;
        text-align: center;
        width: 60px;
    }

    .game--submissionDisabled .game-scoreInputField {
        background: #DDD;
    }

    .game-scoreInputField::-webkit-inner-spin-button,
    .game-scoreInputField::-webkit-outer-spin-button {
        display: none;
    }

    .game-risk {
        padding: 15px;
    }

    .game-risk-title {
        font-style: italic;
    }

    .game-risk-titlePrefix {
        font-weight: bold;
    }

    .game-risk-titlePrefix.rightAnswer {
        background: url('../assets/tick.svg') no-repeat;
        color: #49996f;
        padding-left: 20px;
    }

    .game-risk-titlePrefix.wrongAnswer {
        background: url('../assets/cross.svg') no-repeat;
        color: #b50101;
        padding-left: 20px;
    }

    .game-risk-input {
        font-size: 15px;
    }

    .game-risk-answer {
        padding: 10px;
        text-align: center;
    }

    .game--submissionDisabled .game-risk-answer {
        opacity: 0.5;
    }

    @media (min-width: 550px) {

        .game-risk-input {
            display: flex;
        }

        .game-risk-answer {
            flex: 1 1 0;
        }

        .game-risk-answer:first-child {
            padding-left: 0;
        }

        .game-risk-answer:last-child {
            padding-right: 0;
        }
    }

    .game-risk-answerHeader {
        font-weight: bold;
        margin-bottom: 10px;
    }

    .game-risk-answerNoRisk {
        padding: 8px 0;
    }

    .game-risk-answerChoiceGroup {
        display: flex;
    }

    .game-risk-answerChoice {
        margin: 0;
        padding: 0;
    }

    .game-risk-answerChoice {
        flex: 1 1 0;
        margin: 0;
        padding: 0;
    }

    .game-risk-answerChoice.noAnswer {
        flex: 2 1 0;
    }

    .game-risk-answerChoice input[type="radio"] {
        display: none;
    }

    .game-risk-answerChoice label {
        background: #eee;
        box-sizing: border-box;
        box-shadow: 0 2px 0 #ddd;
        cursor: pointer;
        display: inline-block;
        font-size: 14px;
        font-weight: bold;
        height: 40px;
        line-height: 40px;
        user-select: none;
        width: 100%;
    }

    .game-risk-answerChoice:first-child label {
        border-radius: 5px 0 0 5px;
    }

    .game-risk-answerChoice:last-child label {
        border-radius: 0 5px 5px 0;
    }

    .game-risk-answerChoice:nth-child(2) label {
        border-left: 1px solid #ddd;
        border-right: 1px solid #ddd;
    }

    .game-risk-answerChoice input[type="radio"]:checked + label {
        background: #aaa;
        box-shadow: 0 2px 0 #888;
        color: #fff;
    }

    .game-risk-answerChoice:nth-child(2) input[type="radio"]:checked + label {
        border: none;
    }

    .game-submitZone {
        border-top: 1px dashed #ddd;
        bottom: 0;
        height: 40px;
        left: 0;
        padding: 10px;
        position: absolute;
        right: 0;
    }

    @media (min-width: 500px) {
        .game-submitZone {
            background: #EEE;
            border-top-style: solid;
        }
    }

    .btn.game-submitZone-button {
        background: #4db788;
        border-color: #49996f;
        color: #fff;
        display: block;
        margin: auto;
    }

    .game--submissionDisabled .game-submitZone-button {
        display: none;
    }

    .game-submitZone-savedCheck {
        background: url('../assets/tick.svg') no-repeat;
        height: 25px;
        position: absolute;
        right: 20px;
        bottom: 15px;
        width: 25px;
        z-index: 1;
    }

    .game-pointsExplanation {
        color: #49996f;
        font-weight: bold;
        margin-top: 8px;
        text-align: center;
    }


</style>
