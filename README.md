#!/usr/bin/python3
# -*- coding: utf-8 -*-

## Importation des bibliothèques
from tkinter import *
from tkinter import ttk
from tkinter.messagebox import *
from tkinter.simpledialog import *
import copy
## Fin d'importation des bibliothèques

## Classes
class Application (object) :
    ''' Création de la fenêtre principale '''
    def __init__ (self):
        self.fen = Tk () # Création de la fenêtre
        ## Variables et images
        # VARIABLES
        self.fonte = ('Arial', 14, 'bold')
        self.fonte2 = ('Arial', 20, 'bold')
        self.fonte3 = ('Arial', 40, 'bold')
        self.fonte4 = ('Arial', 16, 'bold')
        self.depart = [0,1,1,1,1,1,1,1,1,1,1,1,1,1,1]
        self.jeu = copy.deepcopy (self.depart)
        self.coordonnees = [[300,60],[240,180],[360,180],[180,300],[300,300],[420,300],[120,420],[240,420],[360,420],[480,420],
                            [60,540],[180,540],[300,540],[420,540],[540,540]]
        self.mouvements = [[0,1,3],[0,2,5],[1,3,6],[1,4,8],[2,4,7],[2,5,9],[3,1,0],[3,4,5],[3,6,10],[3,7,12],
                            [4,7,11],[4,8,13],[5,2,0],[5,4,3],[5,8,12],[5,9,14],[6,3,1],[6,7,8],[7,4,2],
                            [7,8,9],[8,4,1],[8,7,6],[9,5,2],[9,8,7],[10,6,3],[10,11,12],[11,7,4],[11,12,13],
                            [12,7,3],[12,8,5],[12,11,10],[12,13,14],[13,8,4],[13,12,11],[14,9,5],[14,13,12]]
        self.position = -1
        self.ancPosition = -1
        self.nombrePions = 14
        self.partieEnCours = True 
        self.blocage = False
        self.gagne = False
        self.SABLE = "#000000"
        self.ARGENT = "#ffffff"
        self.GUEULE = "#e21313"
        self.AZUR = "#007fff"
        self.OR = "#ffd700"
        self.SINOPLE = "#149414"
        # IMAGES
        self.imgPion = PhotoImage (file = "images\\pion.png")
        self.imgPionSelection = PhotoImage (file = "images\\pion_selection.png")
        self.imgPlateau = PhotoImage (file = "images\\plateau.png")
        self.imgLogo = PhotoImage (file = 'images\\logo_128.png')
        ## Fin variables et images
        
        ## Styles
        self.s = ttk.Style ()
        self.s.theme_use ("vista")
        self.s.map ("BW.TButton", background = [('focus', self.SINOPLE), ('!focus', self.GUEULE)])
        self.s.map ("BW2.TButton", background = [('focus', self.SINOPLE), ('!focus', self.GUEULE)])
        self.s.configure ("BW.TButton", font = self.fonte4, background = self.ARGENT, foreground = self.SABLE, borderwidht = 4, anchor = CENTER, justify = CENTER)
        self.s.configure ("BW2.TButton", font = self.fonte2, background = self.OR, foreground = self.SINOPLE, borderwidht = 4, anchor = CENTER, justify = CENTER)
        self.s.configure ("BW.TLabel", font = self.fonte, background = self.AZUR, foreground = self.ARGENT, justify = CENTER, highlightthickness = 0, anchor = CENTER, border = 0)
        ## Fin styles
        
        ## Intérieur fenêtre
        # 0. Généralités
        self.fen.title("TRIANGLE SOLITAIRE")
        self.fen.resizable(height = False, width = False)
        self.fen.configure(bg = self.AZUR)
        self.fen.iconphoto(True, PhotoImage (file = "images\\logo_128.png"))
        # 1. Jeu
        self.frameJeu = Frame (self.fen, highlightthickness = 0, bg = self.AZUR, width = 900, height = 871)
        self.frameJeu.grid (row = 0, column = 0)
        self.frameJeu.grid_propagate (0)
        self.frameJeuTitre = Frame(self.frameJeu, bg = self.OR, highlightthickness = 0, width = 900, height = 128)
        self.frameJeuTitre.grid(row = 0, column = 0)
        self.frameJeuTitre.grid_propagate (0)
        self.canvasJeuTitreGauche = Canvas(self.frameJeuTitre, bg = self.OR, highlightthickness = 0, width = 128, height = 128)
        self.canvasJeuTitreGauche.grid(row = 0, column = 0)
        self.canvasJeuTitreGauche.create_image(0, 0, image = self.imgLogo, anchor = NW)
        self.labelJeuTitre = ttk.Label(self.frameJeuTitre, style = "BW.TLabel", text = "TRIANGLE SOLITAIRE\nJEU", width = 20)
        self.labelJeuTitre.grid(row = 0, column = 1, padx = 30)
        self.labelJeuTitre.configure(background = self.OR, foreground = self.SINOPLE, font = self.fonte3)
        self.canvasJeuTitreDroite = Canvas(self.frameJeuTitre, bg = self.OR, highlightthickness = 0, width = 128, height = 128)
        self.canvasJeuTitreDroite.grid(row = 0, column = 2)
        self.canvasJeuTitreDroite.create_image(0, 0, image = self.imgLogo, anchor = NW)
        self.frameJeuJeu = Frame (self.frameJeu, highlightthickness = 0, bg = self.AZUR, height = 743, width = 900)
        self.frameJeuJeu.grid (row = 1, column = 0)
        self.frameJeuJeu.grid_propagate (0)        
        self.canvasJeuJeu = Canvas (self.frameJeuJeu, bg = self.AZUR, highlightthickness = 0, width = 600, height = 600)
        self.canvasJeuJeu.grid (row = 0, column = 0 , pady = 20)
        self.affichage ()
        self.frameJeuAction = LabelFrame (self.frameJeuJeu, bg = self.AZUR, fg = self.OR, bd = 3, relief = RIDGE, labelanchor = N, text = "Actions", font = self.fonte4)
        self.frameJeuAction.grid (row = 1, column = 0, padx = 18, pady = (0,20))
        self.btNP = ttk.Button (self.frameJeuAction, style = "BW.TButton", text = "Nouvelle Partie", width = 16, command = self.nouvelle_partie)
        self.btNP.grid (row = 0, column = 0, pady = 10, padx = 10)
        self.btAide = ttk.Button (self.frameJeuAction, style = "BW.TButton", text = "Aide", width = 16, command = self.jeu_vers_aide)
        self.btAide.grid (row = 0, column = 1, pady = 10, padx = 0)
        self.btAP = ttk.Button (self.frameJeuAction, style = "BW.TButton", text = "À Propos", width = 16, command = self.jeu_vers_ap)
        self.btAP.grid (row = 0, column = 2, pady = 10, padx = 10)
        self.btQuitter = ttk.Button (self.frameJeuAction, style = "BW.TButton", text = "Quitter", width = 16, command = self.quitter)
        self.btQuitter.grid (row = 0, column = 3, pady = 10, padx = (0,10))
        
        # 2. À Propos
        self.frameAP = Frame (self.fen, highlightthickness = 0, bg = self.AZUR, height = 871, width = 900)
        self.frameAP.grid (row = 0, column = 0)
        self.frameAP.grid_propagate (0)
        self.frameAPTitre = Frame(self.frameAP, bg = self.OR, highlightthickness = 0, height = 128, width = 900)
        self.frameAPTitre.grid(row = 0, column = 0)
        self.frameAPTitre.grid_propagate (0)
        self.canvasAPTitreGauche = Canvas(self.frameAPTitre, bg = self.OR, highlightthickness = 0, width = 128, height = 128)
        self.canvasAPTitreGauche.grid(row = 0, column = 0)
        self.canvasAPTitreGauche.create_image(0, 0, image = self.imgLogo, anchor = NW)
        self.labelAPTitre = ttk.Label(self.frameAPTitre, style = "BW.TLabel", text = "TRIANGLE SOLITAIRE\nÀ PROPOS", width = 20)
        self.labelAPTitre.grid(row = 0, column = 1, padx = 30)
        self.labelAPTitre.configure(background = self.OR, foreground = self.SINOPLE, font = self.fonte3)
        self.canvasAPTitreDroite = Canvas(self.frameAPTitre, bg = self.OR, highlightthickness = 0, width = 128, height = 128)
        self.canvasAPTitreDroite.grid(row = 0, column = 2)
        self.canvasAPTitreDroite.create_image(0, 0, image = self.imgLogo, anchor = NW)
        self.frameAPAP = Frame(self.frameAP, bg = self.AZUR, highlightthickness = 0, height = 743, width = 900)
        self.frameAPAP.grid (row = 1, column = 0)
        self.frameAPAP.grid_propagate (0)
        self.labelAPAP = ttk.Label(self.frameAPAP, style = "BW.TLabel", text = "")
        self.labelAPAP.grid (row = 0, column = 0, padx = 96, pady = 160)
        self.txtAP = "© copyleft - Bruno CLÉVENOT - Novembre 2023\n\n"
        self.txtAP += "Jeu programmé en Python 3.7 par Bruno CLÉVENOT.\n\n"
        self.txtAP += "Graphismes par Bruno CLÉVENOT.\n\n"
        self.txtAP += "Code et graphismes distribués sous licence GPL3."
        self.labelAPAP.configure (text = self.txtAP, font = self.fonte2)
        self.btRetourAP = ttk.Button (self.frameAPAP, text = "Retour Menu", style = "BW.TButton", width = 13, command = self.ap_vers_jeu)
        self.btRetourAP.grid (row = 1, column = 0)
        self.frameAP.grid_remove ()
        
        # 3. Aide
        self.frameAide = Frame (self.fen, highlightthickness = 0, bg = self.AZUR, height = 871, width = 900)
        self.frameAide.grid (row = 0, column = 0)
        self.frameAide.grid_propagate (0)
        self.frameAideTitre = Frame(self.frameAide, bg = self.OR, highlightthickness = 0, height = 128, width = 900)
        self.frameAideTitre.grid(row = 0, column = 0)
        self.frameAideTitre.grid_propagate (0)
        self.canvasAideTitreGauche = Canvas(self.frameAideTitre, bg = self.OR, highlightthickness = 0, width = 128, height = 128)
        self.canvasAideTitreGauche.grid(row = 0, column = 0)
        self.canvasAideTitreGauche.create_image(0, 0, image = self.imgLogo, anchor = NW)
        self.labelAideTitre = ttk.Label(self.frameAideTitre, style = "BW.TLabel", text = "TRIANGLE SOLITAIRE\nAIDE", width = 20)
        self.labelAideTitre.grid(row = 0, column = 1, padx = 30)
        self.labelAideTitre.configure(background = self.OR, foreground = self.SINOPLE, font = self.fonte3)
        self.canvasAideTitreDroite = Canvas(self.frameAideTitre, bg = self.OR, highlightthickness = 0, width = 128, height = 128)
        self.canvasAideTitreDroite.grid(row = 0, column = 2)
        self.canvasAideTitreDroite.create_image(0, 0, image = self.imgLogo, anchor = NW)
        self.frameAideAide = Frame(self.frameAide, bg = self.AZUR, highlightthickness = 0, height = 743, width = 900)
        self.frameAideAide.grid (row = 1, column = 0)
        self.frameAideAide.grid_propagate (0)
        self.labelAideAide = ttk.Label(self.frameAideAide, style = "BW.TLabel", text = "")
        self.labelAideAide.grid (row = 0, column = 0, padx = 14, pady = 107)
        self.txtAide = "BUT :\n"
        self.txtAide += "Éliminer les pions pour qu'il n'en reste plus qu'un.\n\n"
        self.txtAide += "CONTRAINTES :\n"
        self.txtAide += "Pour éliminer un pion, un autre pion adjacent doit sauter par\ndessus selon une ligne tracée, sous réserve que la case suivant le\n"
        self.txtAide += "pion à prendre soit vide.\n\n"
        self.txtAide += "COMMENT JOUER :\n"
        self.txtAide += "Le joueur séléctionne un pion à déplacer, puis clique sur une\ncase vide : si le mouvement est valide, le pion sélectionné se\n"
        self.txtAide += "déplace et le pion pris disparaît."
        self.labelAideAide.configure (text = self.txtAide, font = self.fonte2, justify = LEFT)
        self.btRetourAide = ttk.Button (self.frameAideAide, text = "Retour Menu", style = "BW.TButton", width = 13, command = self.aide_vers_jeu)
        self.btRetourAide.grid (row = 1, column = 0)
        self.frameAide.grid_remove ()
        
        ## Fonctions Bind
        self.canvasJeuJeu.bind ("<Button-1>", self.clic)
        self.fen.protocol ("WM_DELETE_WINDOW", self.quitter)
        ## Fin fonction Bind
        
        self.fen.update ()
        ## Fin fenêtre 
        
        ## Placement de la fenêtre
        w = self.fen.winfo_width ()
        h = self.fen.winfo_height ()
        ws = self.fen.winfo_screenwidth()
        hs = self.fen.winfo_screenheight()
        a = (ws - w) // 2
        b = (hs - h) // 2
        self.fen.geometry ('%dx%d+%d+%d' % (w, h, a, b))
        ## Fin placement de la fenêtre
        
        self.fen.mainloop ()
        
    ## Fonctions
    def jeu_vers_aide (self) :
        self.frameJeu.grid_remove ()
        self.frameAide.grid ()
    
    def aide_vers_jeu (self) :
        self.frameAide.grid_remove ()
        self.frameJeu.grid ()
    
    def jeu_vers_ap (self) :
        self.frameJeu.grid_remove ()
        self.frameAP.grid ()
        
    def ap_vers_jeu (self) :
        self.frameAP.grid_remove ()
        self.frameJeu.grid ()
    
    def quitter (self):
        if self.partieEnCours == True :
            rep = rep = askyesno ("Jeu en cours","Une partie est en cours !\n\nVoulez-vous vraiment quitter ? \n Cliquer sur « Oui » pour finir")
            if rep :
                self.fen.destroy ()
        else :
            self.fen.destroy ()
    
    def nouvelle_partie (self) :
        if self.partieEnCours == True :
            rep = askyesno ("Partie en Cours", "Une partie est en cours !!\nSouhaitez-vous l'interrompre et en commencez une autre ?")
            if not rep :
                return
        self.jeu [:] =[]
        self.jeu = copy.deepcopy (self.depart)
        self.partieEnCours = True
        self.nombrePions = 14
        self.position = -1
        self.ancPosition = -1
        self.blocage = False
        self.gagne = False
        self.affichage ()
    
    def affichage (self) :
        self.canvasJeuJeu.delete (ALL)
        self.canvasJeuJeu.create_image (300,300,image = self.imgPlateau)
        for i in range (15) :
            if self.jeu [i] == 1 :
                self.canvasJeuJeu.create_image (self.coordonnees[i][0], self.coordonnees[i][1], image = self.imgPion)
            elif self.jeu [i] == 10 :
                self.canvasJeuJeu.create_image (self.coordonnees[i][0], self.coordonnees[i][1], image = self.imgPionSelection)
    
    def clic (self, event) :
        if self.partieEnCours == True :
            for i in range (15) :
                if self.coordonnees[i][0]-30 <= event.x <= self.coordonnees[i][0]+30 and self.coordonnees[i][1]-30 <= event.y <= self.coordonnees[i][1]+30 :
                    if self.jeu [i] == 1 :
                        self.jeu [i] = 10
                        self.ancPosition = i
                        for j in range (15) :
                            if j != i and self.jeu [j] == 10 :
                                self.jeu [j] = 1
                                break
                        self.affichage ()
                        break
                    elif self.jeu [i] == 0 :
                        if self.ancPosition == -1 :
                            showinfo ("Pas de pion sélectionné", "Merci de sélectionner un pion avant déplacement !!")
                        else :
                            self.position = i
                            for k in range (36) :                            
                                if self.mouvements [k][0] == self.ancPosition and self.mouvements [k][2] == self.position :
                                    if self.jeu [self.mouvements [k][1]] == 1 :
                                        self.jeu [self.mouvements [k][1]] = 0
                                        self.jeu [self.position] = 1
                                        self.jeu [self.ancPosition] = 0
                                        self.ancPosition = -1
                                        self.position = -1
                                        self.affichage ()
                                        self.nombrePions -= 1
                                        if self.nombrePions == 1 :
                                            self.partieEnCours = False
                                            self.gagne = True
                                            showinfo ("Partie gagnée", "Félicitation !!\n Vous avez fini le jeu !!")
                                        else :
                                            self.blocage = self.verif_blocage ()
                                            if self.blocage == True :
                                                self.partieEnCours = False
                                                showinfo ("Partie bloquée", "Plus aucun mouvement possible !\nVous avez perdu !!")
                                        break
                                        
    def verif_blocage (self) :
        bloc = True
        for i in range (15) :
            if self.jeu [i] == 1:
                for j in range (36) :
                    if self.mouvements [j][0] == i :
                        if self.jeu [self.mouvements [j][1]] == 1 and self.jeu [self.mouvements [j][2]] == 0 :
                            bloc = False
                            return bloc
        return bloc
