<template>

    <div class="page--faq">
        <card-list simple class="welcomeAndLogin">
            <card class="card--welcome">
                <h2 class="card-title">Bienvenue !</h2>
                <p>Envie de pronostiquer des matchs entre amis ?</p>
                <p><strong>
                    Huit parfait</strong> remplace la bonne vieille feuille Excel partagée par un site accessible sur
                    ordinateur, tablette et téléphone pour le plaisir de tous.
                </p>
                <ol>
                    <li>Créez un ou plusieurs groupes</li>
                    <li>Invitez des amis</li>
                    <li>Pronostiquez les matchs</li>
                    <li>Surveillez le classement...</li>
                    <li>... et amusez vous bien ;-)</li>
                </ol>
            </card>
            <card class="card--login" v-if="user == null">
                <h2 class="card-title">Connexion</h2>
                <p>
                    Pour vous éviter de créer un n-ième mot de passe, la connexion se fait avec un site externe.
                </p>
                <p class="card--login-allBtn">
                    <a class="loginBtn loginBtn--facebook" href="/auth/facebook">avec Facebook</a>
                    <a class="loginBtn loginBtn--google" href="/auth/google">avec Google</a>
                    <a class="loginBtn loginBtn--twitter" href="/auth/twitter">avec Twitter</a>
                </p>
            </card>
        </card-list>

        <card>
            <h2 class="card-title">Atteindre le Huit Parfait&nbsp;!</h2>

            <p>Le but du jeu est bien entendu d'avoir le plus gros ... score !</p>
            <p>
                Pour chaque match, vous pouvez gagner jusqu'à 8 points : le fameux <strong>Huit Parfait</strong> !
                Si vous êtes très mauvais, vous pouvez aussi perdre jusqu'à 3 points, ce qui n'est pas cool.
            </p>
            <p>Vous n'avez que 3 choses à pronostiquer :</p>
            <ol>
                <li>Le nombre de buts de l'équipe A</li>
                <li>Le nombre de buts de l'équipe B</li>
                <li>Est-ce que la "risquette" va se réaliser ?</li>
            </ol>
        </card>

        <card>
            <h2 class="card-title">Comment gagner des points ?</h2>
            <p>
                Pour gagner des points il faut être bon ... ou avoir beaucoup de chance.
                Voici le détails :
            </p>
            <table class="table">
                <tr>
                    <td>Issue du match trouvée (vainqueur ou match nul)</td>
                    <td class="scorePoints">3 pts</td>
                </tr>
                <tr>
                    <td>Nombre de buts de l'équipe A trouvé (hors <abbr title="Tirs Aux Buts">T.A.B.</abbr>)
                    </td>
                    <td class="scorePoints">1 pt</td>
                </tr>
                <tr>
                    <td>Nombre de buts de l'équipe B trouvé (hors <abbr title="Tirs Aux Buts">T.A.B.</abbr>)
                    </td>
                    <td class="scorePoints">1 pt</td>
                </tr>
                <tr>
                    <td>Risquette gagnée</td>
                    <td class="scorePoints">+1 à +3 pts</td>
                </tr>
                <tr>
                    <td>Risquette non jouée (poule mouillée)</td>
                    <td class="scorePoints">0 pt</td>
                </tr>
                <tr>
                    <td>Risquette perdue</td>
                    <td class="scorePoints">−1 à −3 pts</td>
                </tr>
                <tr class="totalPoints">
                    <td>TOTAL</td>
                    <td class="scorePoints">−3 à +8 pts</td>
                </tr>
            </table>
            <p>
                Si vous avez le nombre de buts exact pour les deux équipes et que vous gagnez la risquette maximum,
                vous obtenez un <strong>Huit Parfait&nbsp;!</strong>
            </p>
        </card>

    <card>
        <h2 class="card-title">Mais au fait, c'est quoi une risquette ?</h2>
        <p>
            Une risquette vous permet de "risquer" des points sur un fait de match prédéfini.
        </p>
        <p>
            Par exemple, pour le match "France - Australie", on vous propose une affirmation telle que
            <strong>But du pied gauche</strong>.
            À vous de pronostiquer si cette risquette sera VRAIE ou FAUSSE. Vous pouvez risquer de 1 à 3 points.
            Si à la fin du match vous aviez raison, vous gagnez les points risqués, dans le cas contraire vous perdez
            les points (et votre honneur).
        </p>
        <p>
            Si vous ne vous sentez pas l'âme d'un aventurier, vous pouvez choisir de ne rien risquer.
        </p>
    </card>

        <card>
            <h2 class="card-title">Règles</h2>
            <p>
                Vous devez soumettre votre pronostic au moins une minute avant le match.
                Vous pouvez changer d'avis autant de fois que vous voulez avant.
                C'est le dernier pronostic qui est enregistré.
            </p>
            <p>
                L'éventuelle séance de tirs au but n'est pas comptée pour comparer le score à votre pronostic.
                Par exemple, le match "France&nbsp;-&nbsp;Brésil" se termine avec
                <strong>1&nbsp;-&nbsp;1</strong> à la fin des prolongations et le Brésil gagne la séance de tirs au but
                <strong>3&nbsp;-&nbsp;2</strong>.
            </p>
            <ul>
                <li>
                    Si vous pronostiquez <strong>0&nbsp;-&nbsp;0</strong>, <strong>1&nbsp;-&nbsp;1</strong>,
                    <strong>2&nbsp;-&nbsp;2</strong>... vous avez les 3 points pour <em>"issue du match trouvée"</em>.
                </li>
                <li>
                    Si vous pronostiquez
                    <strong>2&nbsp;-&nbsp;1</strong> pour le Brésil, même si celui-ci a gagné le match aux tirs au but,
                    vous n'avez pas les 3 points pour
                    <em>"issue du match trouvée"</em>.
                </li>
            </ul>
        </card>

        <card>
            <h2 class="card-title">Hébergement</h2>
            <p>
                <strong>Huit Parfait</strong> est gracieusement hébergé et automatisé chez <a href="https://www.clever-cloud.com/" target="_blank">Clever Cloud</a>.
                Sur cette plateforme, nous avons pu déployer automatiquement nos 3 applications <strong>node.js</strong>, notre cache <strong>Redis</strong> et notre base de données <strong>PostgreSQL</strong>.
                <img class="clever-cloud-logo" src="../assets/clever-cloud.svg" alt="Clever Cloud Logo">
            </p>
        </card>

        <card>
            <h2 class="card-title">À propos du site</h2>
            <p>
                <strong>Huit Parfait</strong> est un site éphémère qui revient tous les 2 ans depuis 2014.
                Cette année, les développements sont assurés par <a href="https://twitter.com/hsablonniere" target="_blank">Hubert Sablonnière</a> et <a href="https://twitter.com/mghignet" target="_blank">Maxime Ghignet</a>.
            </p>
            <h3>Vie privée</h3>
            <p>
                Afin de respecter au mieux votre vie privée et aussi parce-que votre email, date de naissance et opinions politiques, <em>on s'en ****</em>, nous avons décidé de stocker le strict minimum dans notre base de données.
            </p>
            <p>
                Nous récupérons et stockons votre avatar et votre "nom prénom" depuis le réseau social utilisé pour la connexion, libre à vous d'éditer ces informations dans votre profil.
                Par défaut, ce "nom prénom" sera uniquement visible par vos amis (joueurs présents dans les mêmes groupes que vous).
                Dans le classement général (visible par tous les joueurs), vous apparaîtrez avec un avatar et pseudo anonyme comme "Escargot fou" ou "Lièvre sympa".
                Si vous êtes un dieu ou une déesse du prono, peut-être voudriez vous apparaître publiquement dans le classement général en désactivant ce mode anonyme.
            </p>
            <h3>Remarques et questions</h3>
            <p>
                Si vous avez la moindre remarque ou question sur le site, les règles etc... n'hésitez pas à contacter
                l'équipe.
                Nous avons réalisé ce site sur notre temps libre pour nous amuser et si possible pour vous permettre de vous
                amuser aussi.
                Il contient probablement quelques bugs, si c'est le cas, soyez indulgent et signalez les ;-)
            </p>
            <p>
                Huit Parfait est un site de pronostics de foot entre amis.
                Il ne s'agit en aucun cas d'un site de paris ou de jeux d'argent.
            </p>
            <p>
                Si le site ne fonctionne pas bien sur votre navigateur, il faut peut-être le mettre à jour...
            </p>
            <p class="btnBar">
                <link-btn href="mailto:huitparfait@gmail.com">huitparfait@gmail.com</link-btn>
                <link-btn href="https://twitter.com/huitparfait">@huitparfait</link-btn>
                <link-btn href="https://github.com/huitparfait">Github</link-btn>
            </p>
        </card>
    </div>

</template>

<script type="text/babel">
    export default {
        data() {
            return {
                user: this.$select('user'),
            }
        },
    }
</script>

<style scoped>

    .card--welcome {
        flex-basis: 100%;
    }

    .card--login {
        flex-basis: 400px;
        overflow: visible;
    }

    @media (min-width: 850px) {
        .welcomeAndLogin {
            flex-wrap: nowrap;
        }
    }

    @media (min-width: 500px) and (max-width: 849px) {
        .card--login-allBtn {
            display: flex;
            justify-content: center;
        }
    }

    .btnBar {
        margin-top: 30px;
        text-align: right;
    }

    .loginBtn,
    .contactBtn {
        border: 1px solid #333;
        border-bottom-width: 2px;
        border-radius: 3px;
        color: #fff;
        font-weight: bold;
        padding: 8px 15px;
        margin: 20px 0;
        text-align: center;
        text-decoration: none;
        white-space: nowrap;
    }

    .loginBtn {
        background-size: contain;
        background-repeat: no-repeat;
        background-position: center center;
        display: block;
        overflow: hidden;
        text-indent: -2000px;
    }

    .loginBtn img {
        height: 50px;
    }

    .loginBtn--facebook {
        background-color: #3b5998;
        border-color: #2c4370;
        background-image: url(../assets/koobecaf.svg);
    }

    .loginBtn--google {
        background-color: #4285F4;
        border-color: #3971cc;
        background-image: url(../assets/google.svg);
    }

    .loginBtn--twitter {
        background-color: #1da1f2;
        border-color: #1c90dc;
        background-image: url(../assets/rettiwt.svg);
    }

    .contactBtn {
        background-color: #4db788;
        border-color: #49996f;
    }

    @media (min-width: 500px) and (max-width: 849px) {
        .loginBtn {
            margin: 0 10px;
            min-width: 100px;
        }
    }

    .card-title {
        color: #49996f;
        margin-top: 0;
    }

    ol,
    ul {
        padding-left: 25px;
    }

    abbr {
        cursor: help;
    }

    table {
        border-spacing: 0;
        border-collapse: collapse;
        width: 100%;
    }

    td {
        border-top: 1px solid #eee;
        padding: 5px 10px;
    }

    tr:nth-child(2n+1) td {
        background-color: #f9f9f9;
    }

    tr.totalPoints td {
        border-bottom: 1px dashed #aaa;
        border-top: 1px dashed #aaa;
        color: #333;
        font-weight: bold;
    }

    .scorePoints {
        color: #49996f;
        font-weight: bold;
        white-space: nowrap;
    }

    .clever-cloud-logo {
        display: block;
        height: 50px;
        margin-top: 15px;
    }

</style>
