# Faire des jeux Adventuron games en Français
Comment déveloper un jeu avec Adventuron en Françias (notes personnelles - traduction)

Ces notes personnelles concernent Aventuron BetaBeta 1.0.0 v77, il est possible que certaines choses aient pu changer depuis. Consulter la documentation d'Adventuron pour vous assurer que ces recommendations soient encore valides.

## Langue

Définissez la langue comme [Spanish]. Adventuron supportant l'espagnol, ceci peut être utile pour les versions françaises (comme les accents). En utilisant la version espagnole, Adventuron peut distinguer plus facilement les différents noms dans la phrase de commande, même si le joueur utilise "avec" ou autres prépositions, qui n'existent pas en espagnol.

`language = spanish`

## Accents

Certains joueurs n'utiliseront pas d'accents dans leurs commandes, mais il est tout de même possible d'include ces alternatives. 
Pas besoin de vous souciez pour á é í ó ú, vu qu'Adventuron les a déjà inclus pour la version espagnole. Mais n'oubliez pas les noms ayant d'autres actions. Il est possible de faire ceci de deux manières:

Ajouter ces versions alternatives dans [vocabulary], très utile si le verbe ou nom est utilisé à plusieurs reprises dans le jeu. Lorsque ceci est défini, voux avez seulement besoin d'inclure qu'un seul des alias dans [match].

```
vocabulary {
   : verb aliases = [brûler, bruler];      
   : noun aliases = [collègue, collegue];   
   : noun aliases = [baïonnette, baionnette];
   : noun aliases = [boîte, boite];
   : noun aliases = [cratère, cratere];
   : noun aliases = [forêt, foret];
   : noun aliases = [mèche, meche];   
}
```

Ou vous pouvez simplement dupliquer les commandes dans [match], ce qui peut être utile si le verbe ou le nom n'est pas beaucoup utilisé.

```
: match "ouvrir boîte; ouvrir boite" {
}
```

## Commandes basiques

Certaines commandes basiques doivent être aussi ajoutée comme synonymes de leurs homologues espagnols.

```
vocabulary {
   : verb aliases = [s, sud];
   : verb aliases = [n, nord];
   : verb aliases = [e, est];
   : verb aliases = [o, ouest];
   : verb aliases = [x, examiner, inspecter];
   : verb aliases = [r, m, regarder];
   : verb aliases = [i, inventaire];
   : verb aliases = [salvar, sauver, sauvegarder];
   : verb aliases = [cargar, charger];
   : verb aliases = [p, prendre, saisir, agripper, arracher, attraper, emparer, happer, ramasser, obtenir, approprier, confisquer, enlever, collecter, récupérer];
   : verb aliases = [dejar, drop, laisser, quitter, poser, jeter, déposer];   
   : verb aliases = [aide, help, ayuda, hint, indice, indication, piste, manuel, instruction, guide];
}
```

## Verbes pronominaux
Certains joueurs préfèrent utiliser des verbes pronominaux, comme [SE LEVER]. Il est possible to contourner ce problem et réécrivant la phrase de commande pour supprimer [SE]. Dans chaque salle, vous aurez besoin d'utiliser une [subroutine].

```
on_command {
      : gosub "sentence_rewriting";
	 More code
}
```
Par example, voici une subroutine que vous pouves utiliser, celle-ci traitant aussi d'autres commandes indiquées pas le joueur.

```
subroutines {
   sentence_rewriting : subroutine {
      // transform look into examine if it is look something
      : if (verb_is "regarder" && !noun1_is "") {
         : set_sentence "examiner $1";
      }
      // pronominal verbs
      : if (verb_is "me" || verb_is "te" || verb_is "se" || verb_is "nous" || verb_is "vous") {
         : if (noun1_is "regarder" || noun1_is "examiner") {
            : set_sentence "examiner moi";
            // (noun1_is "tirer" || !noun2_is "") did not work so we check the exact sentence
         } : else_if (noun1_is "casser" || sentence_raw() == "me tirer" || sentence_raw() == "te tirer" || sentence_raw() == "se tirer" || sentence_raw() == "nous tirer" || sentence_raw() == "vous tirer") {
            : set_sentence "sortir";
            // for the case of "se tirer une balle"
         } : else_if (noun1_is "tirer") {
            : set_sentence "mourir";
         } : else {
            : set_sentence "$1 $2";
         }
      }
   }
}
```
Si le verbe commande par une voyelle, comme par example [S'EXAMINER], la solution ci-dessus ne marchera pas. Mais vous pouvez coder ces verbes dans [match], en ignorant le charactère ’. Comme ci-dessous:
`: match " sexaminer _; mexaminer _" {  }`

## Recommencer ou quitter le jeu
FIN est la commande espagnole pour quitter le jeu (ce qui fait aussi recommencer le jeu). Vu que le comportement par défaut du système demandera au joueur d'utiliser soit "s" pour Oui ou "n" pour Non, il faudra non seulement créer un synonyme pour la commande de fin, et traduire les options pour le joueur:

Dans la section [vocabulary]:
```
   : verb aliases = [fin, recommencer, redémarrer, redémarrage, restart, quit];
```

Et ensuite dans la section [on_command]:

```
   : match "fin -" {
      : add_choice "Oui"  {
         : lose_game;
      }
      : add_choice "No"  {
         : done;
      }
      : choose "<Souhaitez-vous recommencer le jeu ?<6>>" ;
   }
```

## Articles

Lorsque les noms commancent avec une voyelle, comme "arbre", ou sonnant comme une voyelle ("home"), l'article est "l'". Si le joueur indique donc "l'arbre", Adventuron traduira la commande en "larbre", ignorant le charactère '. La solution est d'ajouter un synonyme pour les mots en questions:
```
   : noun aliases = [arbre, larbre];
   : noun aliases = [ennemi, lennemi];
```

Ou:
```
: match "grimper arbre; grimper larbre" {

}
```
Ceci peut conduire à avoir des phrases un peu bizarres, comme "grimper le larbre", mais il sera possible pour le joueur d'utiliser "l'arbre" en jouant.

De la même manière:
```
   : noun aliases = [e, est, lest];
   : noun aliases = [o, ouest, louest];
```
si vous voulez que le joueur puisse utiliser des commandes comme "aller à l'est".

## Popup menu avec le mode desktop
Dans le mode desktop, lorsque vous clicquez droit sur l'écran, un menu pop-up apparait (qui n'est que disponible en espagnol). Vous pouvez essayer de traduire les options directement dans le fichier HTML, ou le désactiver. Pour cette dernière, ajouter le code suivant dans le fichier HTML après les autres lignes <style> … </style>. (Merci à  eツ pour ce code)

`<style>div.menu-popup.advpopuproot{display:none}</style>`

## Messages de Système.
Dans [themes], vous pouvez définir des messages de système. Il s’agit d’une proposition de traduction en français pour les messages de système, pouvant aussi être modifiés dans votre jeu.

```
themes {
   my_theme : theme {
      system_messages {
         transcript_start_message = D'accord
         all_treasures_found_win_game                   = Félicitations, vous avez trouvé tous les trésors. Vous avez gagné !
         already_in_container                           = ${entity} est déjà à l'intérieur du ${entity2}.
         ask_new_game                                   = Souhaitez-vous commencer un nouveau jeu ?
         ask_quit                                       = Souhaitez-vous quitter le jeu ?
         be_more_specific                               = Sois plus précis ...\s
         cannot_carry_any_more                          = Vous avez les mains pleines.
         cannot_carry_any_more_weight                   = C'est trop lourd à porter.
         cant_get_character                             = Pas la meilleure des idées.
         cant_get_scenery                               = Vous ne pouvez pas le prendre.
         cant_see_one_of_those                          = Vous ne pouvez pas en voir un.
         dont_have_one_of_those                         = Vous n'en avez pas un !
         exit_list_additional_exits_are_located_verbose = Des sorties supplémentaires se trouvent\s
         exit_list_end_text                             = .
         exit_list_end_text_verbose                     = .
         exit_list_from_here_you_can_go_verbose         = Vous pouvez aller\s
         exit_list_header_concise                       = Sorties :\s
         exit_list_last_sep_verbose                     = \set\s
         exit_list_sep_verbose                          = ,\s
         exit_list_there_are_no_obvious_exits           = Il n'y a pas de sorties évidentes.
         exit_list_to_the_verbose                       = vers le
         exit_list_you_can_also_go_verbose              = Vous pouvez aussi aller\s
         gamebook_question                              = Sélectionnez une option ...
         i_cant_do_that                                 = Vous ne pouvez pas faire ça.
         invalid_choice                                 = Choix invalide.
         inventory_list_empty                           = Rien
         inventory_list_end_text                        = .
         inventory_list_final_separator                 = \set\s
         inventory_list_header                          = Vous portez:\s
         inventory_list_header_verbose                  = Vous portez\s
         inventory_list_separator                       = ,\s
         it_is_dark                                     = C'est sombre. Vous ne pouvez rien voir.
         must_remove_first                              = Essayez d'abord de l'enlever.
         not_carried                                    = Vous ne pouvez pas ${verb} quelque chose que vous ne portez pas.
         not_present                                    = ${entity} n'est pas là.
         nothing_here                                   = Il n'y a rien ici.
         nothing_to_get                                 = Vous ne le trouvez pas.
         object_list_empty                              = Rien
         object_list_end_text                           = .
         object_list_final_separator                    = \set\s
         object_list_header                             = Vous voyez:\s
         object_list_header_after_initial               = Vous voyez également:\s
         object_list_header_verbose                     = Vous voyez\s
         object_list_header_verbose_after_initial       = Vous voyez également\s
         object_list_separator                          = ,\s
         ok                                             = OK
         on_drop                                        = Vous déposez ${entity}.
         on_get                                         = Vous prenez ${entity}.
         on_put                                         = Vous mettez ${entity} dans ${entity2}.
         on_put_non_container                           = ${entity} n'est pas un conteneur.
         on_put_non_surface                             = ${entity} n'est pas une surface.
         on_remove                                      = Vous enlevez ${entity}.
         on_wear                                        = Vous portez ${entity}.
         post_quit                                      = Vous avez quitté le jeu.
         prompt                                         = >>
         prompt_prefix                                  = Et maintenant?
         question_prompt_char                           = ?
         there_is_nothing_you_can                       = Il n'y a rien que vous puissiez ${verb} pour le moment.
         treasure_message                               = 
         treasure_suffix                                = 
         tutorial_message_prefix                        = Tutoriel:\s
         unknown_noun                                   = Nom inconnu - "${noun}".
         unknown_verb                                   = Verbe inconnu - "${verb}".
         verb_noun_only                                 = Ce jeu ne nécessite que deux entrées de mots.
         worn_suffix                                    = \s(porté)
         you_already_wear                               = Vous portez déjà ça.
         you_are_already_carrying                       = Vous avez déjà ${entity}.
         you_are_not_holding                            = Vous ne tenez pas ${entity}.
         you_cant_go_that_direction                     = Vous ne pouvez pas aller par là.
         you_cant_wear                                  = Vous ne pouvez pas porter ça.
         you_cant_wear_anything_else                    = Vous ne pouvez pas porter autre chose sans enlever quelque chose au préalable.
         you_dont_wear                                  = Vous ne portez pas ça.
         you_see_nothing_special                        = Vous ne voyez rien de spécial.
         you_see_nothing_special_2                      = Vous ne pouvez pas interagir avec cela.
      }
   }
}
```

## Autres Messages de Système
Certains des messages de systèmes ne sont pas inclus dans la liste ci-dessus. Mais vous pouvez les éditer directement dans le fichier HTML. Ceux-ci devront être mis à jour à chaque fois que vous compilez une nouvelle version de votre jeu. Par example les messages de Sauvegarde ou de Chargement ne font pas partis des messages de systèmes, il vous faudra donc les remplacer.

Ceci peut être embettant à faire à chaque fois, mais il est surement possible de créer un script qui fait ces modifications à chaque fois que vous compiler votre jeu. Sinon, il faudra le faire manuellement.

Par example:

`Pantalla Completa` -> Plein écran

Attention! les actions ne sont pas indiqués normalement. Par example, vacío, qui veut dire vide (pour une sauvegarde vide) apparait comme vac\xEDo

`vac\xEDo` -> vide

Pour sauvegarder ou charger des parties, voici les commandes à remplacer:

`Guardar partida` -> Sauvegarder la partie

`vac\xEDo` -> vide

`Volver al juego` -> Retour au jeu

`no hacer nada` -> ne rien faire

`Cancelada.` -> Annulé.

`Juego Guardado En La Ranura` -> Le jeu a été sauvegardé dans le slot

`\xBFSobreescribir el contenido guardado en la ranura` -> Remplacer le contenu enregistré dans le slot

`Cargar juego` -> Charger une partie

`Ranura no v\xE1lida.` -> Slot invalide

`\xBFEst\xE1s seguro de que deseas terminar el juego actual y cargar desde la ranura` -> Êtes-vous sûr de vouloir terminer le jeu en cours et charger le slot

## Orientation
Je n'ai pas essayé ceci, mais le code ci-dessous est apparement une meilleure manière pour traduire les directions.

```
themes {
   mytheme : theme {
      # order of the compass directions matters, and must be translated in full (all 12)
      system_messages {
         compass = [north, northeast, east, southeast, south, southwest,  west, northwest, up, down, inside, outside]
         compass_short = [n, ne, e, se, s, sw, w, nw, u, d, in, out]
      }
   }
}
```
{Note Manon: ça marche très bien!}