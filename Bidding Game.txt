import java.io.*;
import java.util.*;

public class Solution {
/* Head ends here */
static int calculate_bid(int player, int pos,int[] first_moves,int[] second_moves) {
    int starting_money  = 100;
    int my_money_spent  = 0;
    int his_money_spent = 0;
    int my_money_left   = 0;
    int his_money_left  = 0;
    int distance = pos;
    int[] opponent_moves = second_moves;
    boolean drawAdvantageFirst = true;
    
    for (int i = 0; i < first_moves.length; ++i) {
        if (first_moves[i] > second_moves[i]) {
            my_money_spent += first_moves[i]; 
        }
        if (second_moves[i] > first_moves[i]) {
            his_money_spent += second_moves[i];
        }
        if (first_moves[i] == second_moves[i]) {
            if (drawAdvantageFirst) {
                my_money_spent += first_moves[i];
                drawAdvantageFirst = false;
            }
            else {
                his_money_spent += second_moves[i];
                drawAdvantageFirst = true;
            }
        }
    }    
    // We made the assumption that "I" am player 1. That's fine, just switch
    if (player == 2) {
        int temp = my_money_spent; // XOR swap if I'm crazy (I'm not)
        my_money_spent = his_money_spent;
        his_money_spent = temp;
        distance = 10 - pos;
        opponent_moves = first_moves;
    }
    
    my_money_left = starting_money - my_money_spent;
    his_money_left = starting_money - his_money_spent;
    
    /*
    All of the above has been set up code. 
    */
    
    // Just observe the opponent's strategy to start off with, and find out
    // their strategy while making them waste bets
    if (first_moves.length < 3) {
        return 15; // If we win with just 16, that's great. Else, we get it back so no big deal     
                   // This value is purely arbitrary.
    }
    
    if (first_moves.length >= 3) {
        int advantage = my_money_left - his_money_left;
        
        // Determinate conditions (guaranteed win/loss conditions)
        if (opponentAboutToWin(distance)) { 
            return Math.min(my_money_left, his_money_left + 1); // Don't let him win!
        }
        else if (imAboutToWin(distance)) {
            return my_money_left;
        }
        if ((his_money_left + 1 * distance) < my_money_left) { // Guaranteed win condition
            return Math.max(my_money_left/distance, 1);
        }
        // End of determinate conditions
        
        if (steadyBetter(opponent_moves)) {
            return opponent_moves[2] + 1; // really any n < 3 would be okay, 2 is a magic number
        }
        
        if (last3HadDecreasingPattern(opponent_moves)) {
            return Math.max(opponent_moves[opponent_moves.length-1]-1, 1);
        }
        
        return Math.max(my_money_left/distance, 1); // Can't bet 0, so just bet 1; if it's invalid, we lose either way
    }
    
    return 5;
}

// A better is steady if his bets have been all the same
static boolean steadyBetter(int[] moves) {
    boolean prediction = true;
    
    for (int i = 0; i < moves.length-1; ++i) {
        if (moves[i] != moves[i+1]) {
            prediction = false;
        }
    }
    
    return prediction;
}

static boolean opponentAboutToWin(int distance) {
    if (distance == 9) {
        return true;
    }
    else {
        return false;
    }
}

static boolean imAboutToWin(int distance) {
    if (distance == 1) {
        return true;
    }
    else {
        return false;
    }
}

// 2 isn't enough to establish a pattern but 3 is, I would say. 
// If it is the case that they start off their bet fairly strong 
// and become more conservatively because they have less and less money,
// we can return their last move  to win with a relatively small bet,
// assuming their next move will be even smaller than the last one 
static boolean last3HadDecreasingPattern (int[] moves) {
    boolean prediction = false;
    int last = moves.length-1;
    if (moves[last-2] > moves[last-1] && moves[last-1] > moves[last] ) {
        prediction = true;
    }
    
    return prediction;
}


/* Tail starts here */
public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        int player = in.nextInt();                     //1 if first player 2 if second
        int scotch_pos = in.nextInt();                 //position of the scotch
        int bid = 0;                                   //Amount bid by the player
        in.useDelimiter("\n");
        String first_move = in.next();
        String[] move_1 = first_move.split(" ");
        String second_move = in.next();
        String[] move_2= second_move.split(" ");
        int[]first_moves = new int[move_1.length];
        int[] second_moves = new int[move_2.length];
        if(first_move.equals("") == false) {
            for (int i=0;i<move_1.length;i++) {
                first_moves[i] = Integer.parseInt(move_1[i]);
                second_moves[i] = Integer.parseInt(move_2[i]);
            }
        }
        bid = calculate_bid(player,scotch_pos,first_moves,second_moves);
        System.out.print(bid);
    }
}