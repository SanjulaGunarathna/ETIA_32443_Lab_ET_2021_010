#define F_CPU 16000000UL

#include <avr/io.h>
#include <util/delay.h>

int main(void)
{
    // Green LED on pin 6 (PD6) as OUTPUT
    // Red LED on pin 7 (PD7) as OUTPUT
    DDRD |= (1 << PD6) | (1 << PD7);

    // Push buttons on pins 8,9,10,11
    // 8  = PB0
    // 9  = PB1
    // 10 = PB2
    // 11 = PB3

    // Set buttons as INPUT
    DDRB &= ~((1 << PB0) | (1 << PB1) | (1 << PB2) | (1 << PB3));

    // Enable internal pull-up resistors
    PORTB |= (1 << PB0) | (1 << PB1) | (1 << PB2) | (1 << PB3);

    int password[4] = {1, 2, 3, 4};
    int entered[4];

    int index = 0;

    while (1)
    {
        // Button 1 (Pin 8)
        if (!(PINB & (1 << PB0)))
        {
            entered[index] = 1;
            index++;
            _delay_ms(300);
        }

        // Button 2 (Pin 9)
        if (!(PINB & (1 << PB1)))
        {
            entered[index] = 2;
            index++;
            _delay_ms(300);
        }

        // Button 3 (Pin 10)
        if (!(PINB & (1 << PB2)))
        {
            entered[index] = 3;
            index++;
            _delay_ms(300);
        }

        // Button 4 (Pin 11)
        if (!(PINB & (1 << PB3)))
        {
            entered[index] = 4;
            index++;
            _delay_ms(300);
        }

        // Check password after 4 entries
        if (index == 4)
        {
            if (
                entered[0] == password[0] &&
                entered[1] == password[1] &&
                entered[2] == password[2] &&
                entered[3] == password[3]
               )
            {
                // Green LED ON (Pin 6)
                PORTD |= (1 << PD6);
                _delay_ms(2000);
                PORTD &= ~(1 << PD6);
            }
            else
            {
                // Red LED ON (Pin 7)
                PORTD |= (1 << PD7);
                _delay_ms(2000);
                PORTD &= ~(1 << PD7);
            }

            index = 0;
        }
    }
}
